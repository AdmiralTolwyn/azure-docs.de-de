---
title: "Beispiele für Machine Learning mit Spark MLlib auf HDInsight – Azure | Microsoft-Dokumentation"
description: Erfahren Sie, wie Sie Spark MLlib verwenden, um eine Machine Learning-App zu erstellen, die ein Dataset analysiert, indem eine Klassifizierung durch logistische Regression vorgenommen wird.
keywords: "Spark Machine Learning, Beispiel für Spark Machine Learning"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c0fd4baa-946d-4e03-ad2c-a03491bd90c8
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: nitinme
ms.openlocfilehash: f98659081b991d403b6477196042c6ff3d40bb12
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2017
---
# <a name="use-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Verwenden Sie Spark MLlib zum Erstellen einer Machine Learning-Anwendung und zur Analyse eines Datasets

Lernen Sie, wie man mithilfe von Spark **MLlib** eine Machine-Learning-Anwendung erstellt, um eine einfache Vorhersageanalyse für ein offenes Dataset auszuführen. Aus den in Spark-integrierten Machine Learning-Bibliotheken verwendet dieses Beispiel die *Klassifizierung* durch logistische Regression. 

> [!TIP]
> Dieses Beispiel ist auch als Jupyter-Notebook für einen Spark-Cluster (Linux) verfügbar, den Sie in HDInsight erstellen. In der Notebook-Umgebung können Sie die Python-Ausschnitte direkt im Notebook ausführen. Um das Tutorial innerhalb des Notebooks ausführen zu können, erstellen Sie einen Spark-Cluster und starten Sie ein Jupyter-Notebook (`https://CLUSTERNAME.azurehdinsight.net/jupyter`). Führen Sie dann das Notebook **Spark Machine Learning - Vorhersageanalysen für Lebensmittelkontrolldaten mithilfe von MLlib.ipynb** unter dem **Python**-Ordner aus.
>
>

MLLib ist eine Spark-Kernbibliothek, die viele Hilfsprogramme enthält, die nützlich für Aufgaben aus dem Bereich des Machine Learning sind, darunter befinden sich auch Hilfsprogramme für folgende Aufgaben:

* Klassifizierung
* Regression
* Clustering
* Themenmodellierung
* Singulärwertzerlegung (Singular Value Decomposition, SVD) und Hauptkomponentenanalyse (Principal Component Analysis, PCA)
* Testen von Hypothesen und Berechnen von Beispielstatistiken

## <a name="what-are-classification-and-logistic-regression"></a>Was sind Klassifizierung und logistische Regression?
*Klassifizierung*, eine Aufgabe im Bereich des Machine Learning, ist der Prozess, bei dem Eingabedaten in Kategorien sortiert werden. Der Klassifizierungsalgorithmus hat die Aufgabe, herauszufinden, wie „Labels“ den von Ihnen bereitgestellten Eingabedaten zugeordnet werden. So kann ein Machine-Learning-Algorithmus beispielsweise Börsendaten als Eingabe akzeptieren und die Daten in zwei Kategorien einteilen: Aktien, die Sie verkaufen sollten, und solche, die Sie behalten sollten.

Logistische Regression ist der Algorithmus, den Sie für die Klassifizierung verwenden. Die API für die logistische Regression von Spark ist nützlich für eine *binäre Klassifizierung*oder für die Klassifizierung der Eingabedaten in einer von zwei Gruppen. Weitere Informationen zur logistischen Regression finden Sie in [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

Zusammenfassend gesagt, erzeugt der Prozess der logistischen Regression eine *logistische Funktion* , die verwendet werden kann, um die Wahrscheinlichkeit vorherzusagen, dass ein Eingabevektor zu einer Gruppe oder der anderen gehört.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>Beispiel für Vorhersageanalysen für Lebensmittelkontrolldaten
Sie verwenden in diesem Beispiel Spark, um einige Vorhersageanalysen für Lebensmittelkontrolldaten (**Food_Inspections1.csv**) auszuführen, die über das [Datenportal von Chicago](https://data.cityofchicago.org/) erhoben wurden. Dieses Dataset enthält Informationen zu Lebensmittelkontrollen, die in Chicago durchgeführt wurden, darunter Informationen zu jedem überprüften Betrieb, (ggf.) gefundene Verstöße und die Ergebnisse der Überprüfung. Die CSV-Datendatei ist bereits im Speicherkonto verfügbar, das dem Cluster in **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv** zugeordnet ist.

In den folgenden Schritten entwickeln Sie ein Modell, um zu ermitteln, wie Sie eine Lebensmittelkontrolle erfolgreich bestehen können bzw. wann Sie nicht bestehen.

## <a name="start-building-a-spark-mmlib-machine-learning-app"></a>Erstellen einer Spark-MMLib-MachineLearning-App
1. Klicken Sie im [Azure-Portal](https://portal.azure.com/)im Startmenü auf die Kachel für Ihren Spark-Cluster (sofern Sie die Kachel ans Startmenü angeheftet haben). Sie können auch unter **Alle durchsuchen** > **HDInsight-Cluster** zu Ihrem Cluster navigieren.   
1. Klicken Sie auf dem Blatt für den Spark-Cluster auf **Cluster-Dashboard** und anschließend auf **Jupyter Notebook**. Geben Sie die Administratoranmeldeinformationen für den Cluster ein, wenn Sie dazu aufgefordert werden.

   > [!NOTE]
   > Sie können auch das Jupyter Notebook für Ihren Cluster aufrufen, indem Sie in Ihrem Browser die folgende URL öffnen. Ersetzen Sie **CLUSTERNAME** durch den Namen Ihres Clusters:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
1. Erstellen Sie ein Notebook. Klicken Sie auf **Neu** und dann auf **PySpark**.

    ![Erstellen eines neuen Jupyter-Notebooks](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-create-jupyter.png "Create a new Jupyter notebook")
1. Ein neues Notebook mit dem Namen „Untitled.pynb“ wird erstellt und geöffnet. Klicken Sie oben auf den Namen des Notebooks, und geben Sie einen Anzeigenamen ein.

    ![Angeben eines neuen Namens für das Notebook](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-name-jupyter.png "Angeben eines neuen Namens für das Notebook")
1. Da Sie ein Notebook mit dem PySpark-Kernel erstellt haben, müssen Sie keine Kontexte explizit erstellen. Die Spark- und Hive-Kontexte werden automatisch für Sie erstellt, wenn Sie die erste Codezelle ausführen. Sie können mit der Erstellung Ihrer Machine Learning-Anwendung beginnen, indem Sie die Typen importieren, die für dieses Szenario erforderlich sind. Setzen Sie dazu den Cursor in die Zelle, und drücken Sie **UMSCHALT- + EINGABETASTE**.

        from pyspark.ml import Pipeline
        from pyspark.ml.classification import LogisticRegression
        from pyspark.ml.feature import HashingTF, Tokenizer
        from pyspark.sql import Row
        from pyspark.sql.functions import UserDefinedFunction
        from pyspark.sql.types import *

## <a name="construct-an-input-dataframe"></a>Erstellen eines Eingabedataframes
Wir können `sqlContext` verwenden, um Transformationen strukturierter Daten auszuführen. Die erste Aufgabe besteht darin, die Beispieldaten (**Food_Inspections1.csv**) in einen Spark SQL-*Dataframe* zu laden.

1. Da die Rohdaten im CSV-Format vorliegen, müssen wir den Spark-Kontext verwenden, um jede Zeile der Datei als unstrukturierten Text in den Arbeitsspeicher zu verschieben. Verwenden Sie dann die Python CSV-Bibliothek, um jede Zeile einzeln zu analysieren.

        def csvParse(s):
            import csv
            from StringIO import StringIO
            sio = StringIO(s)
            value = csv.reader(sio).next()
            sio.close()
            return value

        inspections = sc.textFile('wasb:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
                        .map(csvParse)
1. Die CSV-Datei liegt jetzt als ein RDD vor.  Rufen Sie eine Zeile aus dem RDD ab, um das Datenschema zu verstehen.

        inspections.take(1)

    Folgendes sollte angezeigt werden:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [['413707',
          'LUNA PARK INC',
          'LUNA PARK  DAY CARE',
          '2049789',
          "Children's Services Facility",
          'Risk 1 (High)',
          '3250 W FOSTER AVE ',
          'CHICAGO',
          'IL',
          '60625',
          '09/21/2010',
          'License-Task Force',
          'Fail',
          '24. DISH WASHING FACILITIES: PROPERLY DESIGNED, CONSTRUCTED, MAINTAINED, INSTALLED, LOCATED AND OPERATED - Comments: All dishwashing machines must be of a type that complies with all requirements of the plumbing section of the Municipal Code of Chicago and Rules and Regulation of the Board of Health. OBSEVERD THE 3 COMPARTMENT SINK BACKING UP INTO THE 1ST AND 2ND COMPARTMENT WITH CLEAR WATER AND SLOWLY DRAINING OUT. INST NEED HAVE IT REPAIR. CITATION ISSUED, SERIOUS VIOLATION 7-38-030 H000062369-10 COURT DATE 10-28-10 TIME 1 P.M. ROOM 107 400 W. SURPERIOR. | 36. LIGHTING: REQUIRED MINIMUM FOOT-CANDLES OF LIGHT PROVIDED, FIXTURES SHIELDED - Comments: Shielding to protect against broken glass falling into food shall be provided for all artificial lighting sources in preparation, service, and display facilities. LIGHT SHIELD ARE MISSING UNDER HOOD OF  COOKING EQUIPMENT AND NEED TO REPLACE LIGHT UNDER UNIT. 4 LIGHTS ARE OUT IN THE REAR CHILDREN AREA,IN THE KINDERGARDEN CLASS ROOM. 2 LIGHT ARE OUT EAST REAR, LIGHT FRONT WEST ROOM. NEED TO REPLACE ALL LIGHT THAT ARE NOT WORKING. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned. MISSING CEILING TILES WITH STAINS IN WEST,EAST, IN FRONT AREA WEST, AND BY THE 15MOS AREA. NEED TO BE REPLACED. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair. SPLASH GUARDED ARE NEEDED BY THE EXPOSED HAND SINK IN THE KITCHEN AREA | 34. FLOORS: CONSTRUCTED PER CODE, CLEANED, GOOD REPAIR, COVING INSTALLED, DUST-LESS CLEANING METHODS USED - Comments: The floors shall be constructed per code, be smooth and easily cleaned, and be kept clean and in good repair. INST NEED TO ELEVATE ALL FOOD ITEMS 6INCH OFF THE FLOOR 6 INCH AWAY FORM WALL.  ',
          '41.97583445690982',
          '-87.7107455232781',
          '(41.97583445690982, -87.7107455232781)']]
1. Die obigen Ausgabe gewährt einen Einblick in das Schema der Eingabedatei. Sie enthält unter anderem den Namen und die Art jedes Betriebs, die Adresse, die Kontrolldaten und den Speicherort. Wählen Sie einige Spalten aus, die für unsere Vorhersageanalyse nützlich sind, und gruppieren Sie die Ergebnisse als Dataframe, das anschließend zum Erstellen einer temporären Tabelle verwendet wird.

        schema = StructType([
        StructField("id", IntegerType(), False),
        StructField("name", StringType(), False),
        StructField("results", StringType(), False),
        StructField("violations", StringType(), True)])

        df = sqlContext.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
        df.registerTempTable('CountResults')
1. Wir haben jetzt einen *Dataframe* (`df`), für den wir unsere Analyse ausführen können. Wir verfügen außerdem über eine temporäre Tabelle namens **CountResults**. Wir haben vier für uns interessante Spalten in den Dataframe aufgenommen: **ID**, **Name**, **Ergebnisse** und **Verstöße**.

    Rufen Sie eine kleine Teilmenge der Daten ab:

        df.show(5)

    Folgendes sollte angezeigt werden:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        +------+--------------------+-------+--------------------+
        |    id|                name|results|          violations|
        +------+--------------------+-------+--------------------+
        |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
        |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
        |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
        |413708|BENCHMARK HOSPITA...|   Pass|                    |
        |413722|           JJ BURGER|   Pass|                    |
        +------+--------------------+-------+--------------------+

## <a name="understand-the-data"></a>Grundlegendes zu den Daten
1. Verschaffen Sie sich zunächst einen Überblick darüber, was in dem Dataset enthalten ist. Wie lauten z.B. die verschiedenen Werte in der Spalte **results**?

        df.select('results').distinct().show()

    Folgendes sollte angezeigt werden:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        +--------------------+
        |             results|
        +--------------------+
        |                Fail|
        |Business Not Located|
        |                Pass|
        |  Pass w/ Conditions|
        |     Out of Business|
        +--------------------+
1. Eine kurze Visualisierung hilft uns dabei, die Verteilung der Ergebnisse zu begründen. Die Daten befinden sich bereits in der temporären Tabelle **CountResults**. Sie können die folgende SQL-Abfrage in der Tabelle ausführen, um besser zu verstehen, wie die Ergebnisse verteilt werden.

        %%sql -o countResultsdf
        SELECT results, COUNT(results) AS cnt FROM CountResults GROUP BY results

    Durch den Befehl `%%sql` gefolgt von `-o countResultsdf` wird sichergestellt, dass die Ausgabe der Abfrage lokal auf dem Jupyter-Server (in der Regel der Hauptknoten des Clusters) beibehalten wird. Die Ausgabe wird als [Pandas](http://pandas.pydata.org/) -Dataframe mit dem angegebenen Namen **countResultsdf**beibehalten.

    Folgendes sollte angezeigt werden:

    ![SQL-Abfrageausgabe](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "SQL-Abfrageausgabe")

    Weitere Informationen zur `%%sql`-Magic sowie anderen für den PySpark-Kernel verfügbaren Magics finden Sie unter [Verfügbare Kernels für Jupyter Notebooks mit Spark-Clustern unter HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).
1. Sie können auch Matplotlib verwenden, eine Bibliothek zur Visualisierung von Daten, um eine Grafik zu erstellen. Da die Grafik aus dem lokal gespeicherten **countResultsdf**-Dataframe erstellt werden muss, muss der Codeausschnitt mit der `%%local`-Magic beginnen. Dadurch wird sichergestellt, dass der Code lokal auf dem Jupyter-Server ausgeführt wird.

        %%local
        %matplotlib inline
        import matplotlib.pyplot as plt

        labels = countResultsdf['results']
        sizes = countResultsdf['cnt']
        colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
        plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
        plt.axis('equal')

    Folgendes sollte angezeigt werden:

    ![Ausgabe der Spark-Machine Learning-Anwendung im Kreisdiagramm mit fünf unterschiedlichen Messergebnissen](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-1.png "Spark-Machine-Learning-Ergebnisausgaben")
1. Es gibt fünf unterschiedliche Ergebnisse einer Untersuchung:

   * Business not located
   * Fail
   * Pass
   * Pss w/ Conditions
   * Out of Business

     Entwickeln Sie ein Modell, mit dem das Ergebnis einer Lebensmittelkontrolle anhand der Verstöße vorhergesagt werden kann. Da die logistische Regression eine binäre Klassifizierungsmethode ist, ist es sinnvoll, die Daten in zwei Kategorien zu gruppieren: **Fail** und **Pass**. Bei einem „Pass w/ Conditions“ wurde die Kontrolle dennoch bestanden. Beim Trainieren des Modells werden die beiden Ergebnisse als gleichwertig bewertet. Daten mit anderen Ergebnissen („Out of Business“, „Business not found“) sind nicht nützlich, sodass wir sie von unserem Trainingssatz entfernen. Dies sollte kein Problem sein, da diese beiden Kategorien nur einen kleinen Prozentsatz der Ergebnisse ausmachen.
1. Konvertieren Sie nun den bestehenden Dataframe (`df`) in einen neuen Dataframe, wobei jede Kontrolle als Label-Violations-Paar dargestellt wird. In unserem Fall stellt das Label `0.0` ein Nichtbestehen dar, das Label `1.0` steht für das Bestehen der Kontrolle und das Label `-1.0` steht für andere Ergebnisse. Bei der Berechnung des neuen Dataframes filtern wir diese anderen Ergebnisse heraus.

        def labelForResults(s):
            if s == 'Fail':
                return 0.0
            elif s == 'Pass w/ Conditions' or s == 'Pass':
                return 1.0
            else:
                return -1.0
        label = UserDefinedFunction(labelForResults, DoubleType())
        labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')

    Rufen Sie eine Zeile aus den gekennzeichneten Daten ab,um sie genauer betrachten zu können.

        labeledData.take(1)

    Folgendes sollte angezeigt werden:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Erstellen eines logistischen Regressionsmodells anhand des Eingabedataframes
Die letzte Aufgabe besteht darin, die Daten mit Label in ein Format zu konvertieren, das mit der logistische Regression analysiert werden kann. Die Eingabe für einen logistischen Regressionsalgorithmus muss eine Reihe von *Label-Feature-Vektorpaaren* sein, wobei der „Funktionsvektor“ aus Zahlen besteht und den Eingabepunkt darstellt. Daher benötigen wir eine Möglichkeit, um die Spalte „violations“, die nur teilweise strukturiert ist und viele Freitextkommentare enthält, in ein Array reeller Zahlen zu konvertieren, die ein Computer problemlos interpretieren kann.

Ein Standardansatz beim maschinellen Lernen für die Verarbeitung natürlicher Sprache besteht darin, jedem klaren Wort einen „Index“ zuzuweisen und dann einen Vektor an den Algorithmus für maschinelles Lernen zu übergeben, sodass der Wert jedes Indexes die relative Häufigkeit dieses Worts in der Textzeichenfolge enthält.

MLlib bietet eine einfache Möglichkeit um diesen Vorgang auszuführen. Zunächst wird jeder Verstoßzeichenfolge ein Token zugewiesen, um die einzelnen Wörter in jeder Zeichenfolge abzurufen. Verwenden Sie dann eine `HashingTF`, um jeden Satz von Token in einen Funktionsvektor zu konvertieren, der dann an den logistischen Regressionsalgorithmus übergeben werden kann, um ein Modell zu erstellen. Wir müssen alle diese Schritte der Reihe nach über eine „Pipeline“ durchführen.

    tokenizer = Tokenizer(inputCol="violations", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

    model = pipeline.fit(labeledData)

## <a name="evaluate-the-model-on-a-separate-test-dataset"></a>Bewerten des Modells in einem separaten Testdataset
Wir können das zuvor erstellte Modell verwenden, um basierend auf den beobachteten Verstößen *vorherzusagen* , wie die Ergebnisse der neuen Kontrollen ausfallen werden. Wir haben dieses Modell mit dem Dataset **Food_Inspections1.csv** trainiert. Verwenden Sie nun ein zweites Dataset, **Food_Inspections2.csv**, um die Stärke dieses Modells für neue Daten *auszuwerten*. Das zweite Dataset (**Food_Inspections2.csv**) sollte sich bereits im Standardspeichercontainer befinden, der dem Cluster zugeordnet ist.

1. Mit dem folgenden Codeausschnitt wird ein neuer Dataframe erstellt, **predictionsDf**, der die vom Modell generierte Vorhersage enthält. Der Codeausschnitt erstellt basierend auf dem Dataframe ebenfalls eine temporäre Tabelle namens **Predictions**.

        testData = sc.textFile('wasb:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                 .map(csvParse) \
                 .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
        testDf = sqlContext.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
        predictionsDf = model.transform(testDf)
        predictionsDf.registerTempTable('Predictions')
        predictionsDf.columns

    Folgendes sollte angezeigt werden:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        ['id',
         'name',
         'results',
         'violations',
         'words',
         'features',
         'rawPrediction',
         'probability',
         'prediction']
1. Betrachten Sie eine der Vorhersagen. Führen Sie diesen Codeausschnitt aus:

        predictionsDf.take(1)

   Sie sehen die Vorhersage für den ersten Eintrag im Testdataset.
1. Die `model.transform()`-Methode wendet dieselbe Transformation auf alle neuen Daten mit dem gleichen Schema an und erhält eine Vorhersage, wie die Daten klassifiziert werden können. Mit nur einigen einfachen statistischen Daten, gewinnen Sie einen Eindruck davon, wie präzise unsere Vorhersagen waren:

        numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                              (prediction = 1 AND (results = 'Pass' OR
                                                                   results = 'Pass w/ Conditions'))""").count()
        numInspections = predictionsDf.count()

        print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
        print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"

    Die Ausgabe sieht wie folgt aus:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        There were 9315 inspections and there were 8087 successful predictions
        This is a 86.8169618894% success rate

    Durch Verwenden der logistischen Regression mit Spark erhalten Sie ein präzises Modell der Beziehung zwischen Verstoßbeschreibungen auf Englisch und darüber, ob ein bestimmter Betrieb eine Lebensmittelkontrolle bestehen bzw. nicht bestehen würde.

## <a name="create-a-visual-representation-of-the-prediction"></a>Erstellen einer visuellen Darstellung der Vorhersage
Wir können nun eine endgültige Visualisierung erstellen, um uns mit den Ergebnissen dieses Tests auseinanderzusetzen.

1. Wir beginnen mit dem Extrahieren der unterschiedlichen Vorhersagen und Ergebnisse aus der zuvor erstellten temporären Tabelle **Predictions** . Die folgenden Abfragen teilt die Ausgabe in *true_positive*, *false_positive*, *true_negative*, und *false_negative* auf. In den folgenden Abfragen deaktivieren wir die Visualisierung mithilfe von `-q` und speichern auch die Ausgabe (mithilfe von `-o`) als Dataframes, die dann mit der `%%local`-Magic verwendet werden können.

        %%sql -q -o true_positive
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND results = 'Fail'

        %%sql -q -o false_positive
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND (results = 'Pass' OR results = 'Pass w/ Conditions')

        %%sql -q -o true_negative
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND results = 'Fail'

        %%sql -q -o false_negative
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
1. Abschließend verwenden Sie den folgenden Ausschnitt, um die Grafik mithilfe von **Matplotlib**zu generieren.

        %%local
        %matplotlib inline
        import matplotlib.pyplot as plt

        labels = ['True positive', 'False positive', 'True negative', 'False negative']
        sizes = [true_positive['cnt'], false_positive['cnt'], false_negative['cnt'], true_negative['cnt']]
        colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
        plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
        plt.axis('equal')

    Die folgende Ausgabe wird angezeigt.

    ![Spark-Machine Learning-Anwendungsausgabe: Prozentsätze der nicht bestandenen Lebensmittelkontrollen im Kreisdiagramm](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Spark-Machine Learning-Ergebnisausgaben")

    In diesem Diagramm bezieht sich ein „positives“ Ergebnis auf eine nicht bestandene Lebensmittelkontrolle, wohingegen sich ein negatives Ergebnis auf eine bestandene Kontrolle bezieht.

## <a name="shut-down-the-notebook"></a>Herunterfahren des Notebooks
Nach dem Ausführen der Anwendung empfiehlt es sich, das Notebook herunterzufahren, um die Ressourcen freizugeben. Klicken Sie hierzu im Menü **Datei** des Notebooks auf die Option zum **Schließen und Anhalten**. Hierdurch wird das Notebook heruntergefahren und geschlossen.

## <a name="seealso"></a>Weitere Informationen
* [Übersicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Szenarios
* [Spark mit BI: Durchführen interaktiver Datenanalysen mithilfe von Spark in HDInsight mit BI-Tools](apache-spark-use-bi-tools.md)
* [Spark mit Machine Learning: Analysieren von Gebäudetemperaturen mithilfe von Spark in HDInsight und HVAC-Daten](apache-spark-ipython-notebook-machine-learning.md)
* [Spark-Streaming: Erstellen von Echtzeitstreaminganwendungen mithilfe von Spark in HDInsight](apache-spark-eventhub-streaming.md)
* [Websiteprotokollanalyse mithilfe von Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Erstellen und Ausführen von Anwendungen
* [Erstellen einer eigenständigen Anwendung mit Scala](apache-spark-create-standalone-application.md)
* [Remoteausführung von Aufträgen in einem Spark-Cluster mithilfe von Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Tools und Erweiterungen
* [Verwenden des HDInsight-Tools-Plug-Ins für IntelliJ IDEA zum Erstellen und Übermitteln von Spark Scala-Anwendungen](apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Verwenden von HDInsight-Tools-Plug-Ins für IntelliJ IDEA zum Remotedebuggen von Spark-Anwendungen)](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Verwenden von Zeppelin-Notebooks mit einem Spark-Cluster in HDInsight](apache-spark-zeppelin-notebook.md)
* [Verfügbare Kernels für Jupyter-Notebook im Spark-Cluster für HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Verwenden von externen Paketen mit Jupyter Notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installieren von Jupyter Notebook auf Ihrem Computer und Herstellen einer Verbindung zum Apache Spark-Cluster in Azure HDInsight (Vorschau)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Verwalten von Ressourcen
* [Verwalten von Ressourcen für den Apache Spark-Cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight(Nachverfolgen und Debuggen von Aufträgen in einem Apache Spark-Cluster unter HDInsight)](apache-spark-job-debugging.md)
