<properties
	pageTitle="Generieren von Empfehlungen mit Mahout und Linux-basiertem HDInsight | Microsoft Azure"
	description="Erfahren Sie, wie Sie Filmempfehlungen mit der Apache Mahout-Bibliothek für maschinelles Lernen und Linux-basiertem HDInsight (Hadoop) erstellen können."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="jhubbard"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/30/2016"
	ms.author="larryfr"/>

#Erstellen von Filmempfehlungen mithilfe von Apache Mahout mit Linux-basiertem Hadoop in HDInsight

[AZURE.INCLUDE [Mahout-Auswahl](../../includes/hdinsight-selector-mahout.md)]

Erfahren Sie, wie Sie Filmempfehlungen mit der [Apache Mahout](http://mahout.apache.org)-Bibliothek für maschinelles Lernen und Azure HDInsight erstellen können.

Mahout ist eine [Bibliothek für maschinelles Lernen][ml] für Apache Hadoop. Mahout enthält Algorithmen zur Verarbeitung von Daten wie etwa Filtern, Klassifizierung und Clustering. In diesem Artikel verwenden Sie ein Empfehlungsmodul zum Generieren von Filmempfehlungen auf der Grundlage von Filmen, die Ihre Freunde gesehen haben.

> [AZURE.NOTE] Für die in diesem Dokument beschriebenen Schritte ist ein Linux-basierter Hadoop-Cluster in HDInsight erforderlich. Informationen zur Verwendung von Mahout mit einem Windows-basierten Cluster finden Sie unter [Erstellen von Filmempfehlungen mithilfe von Apache Mahout mit Windows-basiertem Hadoop in HDInsight](hdinsight-mahout.md).

##Voraussetzungen

* Einen Linux-basierten Hadoop auf einem HDInsight-Cluster. Informationen zu dessen Erstellung finden Sie unter [Erste Schritte mit Linux-basiertem Hadoop in HDInsight][getstarted].

##Mahout-Versionsverwaltung

Weitere Informationen zu der Version von Mahout, die in Ihrem HDInsight-Cluster enthalten ist, finden Sie unter [HDInsight-Versionen und Hadoop-Komponenten](hdinsight-component-versioning.md).

> [AZURE.WARNING] Es ist zwar möglich, eine andere Version von Mahout in den HDInsight-Cluster hochzuladen, aber nur Komponenten, die mit dem HDInsight-Cluster bereitgestellt werden, werden vollständig unterstützt, und Microsoft Support hilft Ihnen, Probleme im Zusammenhang mit diesen Komponenten zu isolieren und zu beheben.
>
> Für benutzerdefinierte Komponenten steht kommerziell angemessener Support für eine weiterführende Behebung des Problems zur Verfügung. Auf diese Weise kann das Problem behoben werden, ODER Sie werden aufgefordert, verfügbare Kanäle für Open-Source-Technologien in Anspruch zu nehmen, die über umfassende Kenntnisse für diese Technologien verfügen. So können z. B. viele Communitywebsites verwendet werden, wie: das [MSDN-Forum für HDInsight](https://social.msdn.microsoft.com/Forums/azure/de-DE/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Für Apache-Projekte gibt es Projektwebsites auf [http://apache.org](http://apache.org), zum Beispiel: [Hadoop](http://hadoop.apache.org/), [Spark](http://spark.apache.org/).

##<a name="recommendations"></a>Grundlegendes zu Empfehlungen

Eine der von Mahout bereitgestellten Funktionen ist ein Empfehlungsmodul. Dieses Modul akzeptiert Daten im Format `userID`, `itemId` und `prefValue` (Benutzervoreinstellung für das Element). Mahout kann dann eine Analyse des gemeinsamen Vorkommens durchführen: _Benutzer, die eine Vorliebe für ein bestimmtes Element haben, haben auch eine Vorliebe für andere Elemente_. Mahout bestimmt dann Benutzer mit ähnlichen Vorlieben für Elemente, aus denen sich Empfehlungen erstellen lassen.

Im Folgenden sehen Sie ein extrem einfaches Beispiel mit Spielfilmen:

* __Gemeinsames Vorkommen__: Joe, Alice und Bob gefällt _Krieg der Sterne_, _Das Imperium schlägt zurück_ und _Rückkehr der Jedi-Ritter_. Mahout stellt fest, dass Benutzer, denen einer dieser Filme gefällt, auch die beiden anderen mögen.

* __Gemeinsames Vorkommen__: Bob und Alice haben auch _Die dunkle Bedrohung_, _Angriff der Klonkrieger_ und _Die Rache der Sith_ gefallen. Mahout stellt fest, dass Benutzer, denen die vorherigen drei Filme gefallen, auch diese drei mögen.

* __Vergleichbare Empfehlung__: Da Joe die ersten drei Filme gefallen haben, sucht Mahout nach Filmen, die anderen Personen mit ähnlichen Vorlieben gefallen haben, die Joe aber noch nicht gesehen (mit "Gefällt mir" markiert oder bewertet) hat. In diesem Fall empfiehlt Mahout _Die dunkle Bedrohung_, _Angriff der Klonkrieger_ und _Die Rache der Sith_.

###Grundlegendes zu den Daten

Praktischerweise stellt [GroupLens Research][movielens] Bewertungsdaten für Filme in einem Mahout-kompatiblen Format zur Verfügung. Diese Daten sind im Standardspeicher Ihres Clusters unter `/HdiSamples/HdiSamples/MahoutMovieData` verfügbar.

Es existieren zwei Dateien, `moviedb.txt` (Informationen zu den Filmen) und `user-ratings.txt`. Die Datei „user-ratings.txt“ der Benutzerbewertung wird während der Analyse verwendet, während „moviedb.txt“ beim Anzeigen der Ergebnisse der Analyse benutzerfreundliche Textinformationen angibt.

Die Daten in der Datei „user-ratings.txt“ der Benutzerbewertung haben die Struktur von `userID`, `movieID`, `userRating` und `timestamp`, anhand der die Bewertung eines Films durch die einzelnen Benutzer ersichtlich wird. Hier sehen Sie ein Beispiel für die Daten:


    196	242	3	881250949
    186	302	3	891717742
    22	377	1	878887116
    244	51	2	880606923
    166	346	1	886397596

##Ausführen der Analyse

Führen Sie den folgenden Befehl aus, um den Auftrag für die Filmempfehlungen auszuführen:

    mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp

> [AZURE.NOTE] Der Auftrag kann mehrere Minuten dauern und mehrere MapReduce-Jobs einschließen.

##Anzeigen der Ausgabe

1. Sobald der Auftrag abgeschlossen ist, verwenden Sie den folgenden Befehl zum Anzeigen der Ausgabe.

		hdfs dfs -text /example/data/mahoutout/part-r-00000

	Die Ausgabe wird wie folgt angezeigt:

		1	[234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
		2	[282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
		3	[284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
		4	[690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

	Die erste Spalte ist die `userID`. Die in '[' und ']' eingeschlossenen Werte sind `movieId`:`recommendationScore`.

2. Die Ausgabedaten können zusammen mit „moviedb.txt“ verwendet werden, um benutzerfreundlichere Informationen anzuzeigen. Zunächst müssen wir die Dateien lokal mithilfe folgender Befehle kopieren:

		hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
        hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .

	Dadurch werden die Ausgabedaten zusammen mit den Filmdatendateien in eine Datei namens **recommendations.txt** im aktuellen Verzeichnis kopiert.

3. Verwenden Sie den folgenden Befehl, um ein neues Python-Skript zu erstellen, das Filmnamen für die Daten in der Empfehlungsausgabe sucht:

		nano show_recommendations.py

	Verwenden Sie, sobald der Editor geöffnet wird, Folgendes als Inhalt für die Datei:

        #!/usr/bin/env python

        import sys

        if len(sys.argv) != 5:
                print "Arguments: userId userDataFilename movieFilename recommendationFilename"
                sys.exit(1)

        userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

        print "Reading Movies Descriptions"
        movieFile = open(movieFilename)
        movieById = {}
        for line in movieFile:
                tokens = line.split("|")
                movieById[tokens[0]] = tokens[1:]
        movieFile.close()

        print "Reading Rated Movies"
        userDataFile = open(userDataFilename)
        ratedMovieIds = []
        for line in userDataFile:
                tokens = line.split("\t")
                if tokens[0] == userId:
                        ratedMovieIds.append((tokens[1],tokens[2]))
        userDataFile.close()

        print "Reading Recommendations"
        recommendationFile = open(recommendationFilename)
        recommendations = []
        for line in recommendationFile:
                tokens = line.split("\t")
                if tokens[0] == userId:
                        movieIdAndScores = tokens[1].strip("[]\n").split(",")
                        recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
                        break
        recommendationFile.close()

        print "Rated Movies"
        print "------------------------"
        for movieId, rating in ratedMovieIds:
                print "%s, rating=%s" % (movieById[movieId][0], rating)
        print "------------------------"

        print "Recommended Movies"
        print "------------------------"
        for movieId, score in recommendations:
                print "%s, score=%s" % (movieById[movieId][0], score)
        print "------------------------"

	Drücken Sie **STRG + X**, **STRG + Y** und schließlich die **EINGABETASTE**, um die Daten zu speichern.

3. Verwenden Sie folgenden Befehl, um die Datei ausführbar zu machen:

		chmod +x show_recommendations.py

4. Führen Sie das Python-Skript aus. Im Folgenden wird davon ausgegangen, dass Sie sich im Verzeichnis befinden, in das alle Dateien heruntergeladen wurden:

		./show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt

	Dieses sucht nach den für die Benutzer-ID 4 generierten Empfehlungen.

	* Mit der Datei **user-ratings.txt** werden Filme abgerufen, die vom Benutzer bewertet wurden.
	* Mit der Datei **moviedb.txt** werden die Namen der Filme abgerufen.
	* Mit der Datei **recommendations.txt** werden die Filmempfehlungen dieses Benutzers abgerufen.

	Die Ausgabe dieses Befehls sollte ungefähr wie folgt aussehen:

		Reading Movies Descriptions
		Reading Rated Movies
		Reading Recommendations
		Rated Movies
		------------------------
		Mimic (1997), rating=3
		Ulee's Gold (1997), rating=5
		Incognito (1997), rating=5
		One Flew Over the Cuckoo's Nest (1975), rating=4
		Event Horizon (1997), rating=4
		Client, The (1994), rating=3
		Liar Liar (1997), rating=5
		Scream (1996), rating=4
		Star Wars (1977), rating=5
		Wedding Singer, The (1998), rating=5
		Starship Troopers (1997), rating=4
		Air Force One (1997), rating=5
		Conspiracy Theory (1997), rating=3
		Contact (1997), rating=5
		Indiana Jones and the Last Crusade (1989), rating=3
		Desperate Measures (1998), rating=5
		Seven (Se7en) (1995), rating=4
		Cop Land (1997), rating=5
		Lost Highway (1997), rating=5
		Assignment, The (1997), rating=5
		Blues Brothers 2000 (1998), rating=5
		Spawn (1997), rating=2
		Wonderland (1997), rating=5
		In & Out (1997), rating=5
		------------------------
		Recommended Movies
		------------------------
		Seven Years in Tibet (1997), score=5.0
		Indiana Jones and the Last Crusade (1989), score=5.0
		Jaws (1975), score=5.0
		Sense and Sensibility (1995), score=5.0
		Independence Day (ID4) (1996), score=5.0
		My Best Friend's Wedding (1997), score=5.0
		Jerry Maguire (1996), score=5.0
		Scream 2 (1997), score=5.0
		Time to Kill, A (1996), score=5.0
		Rock, The (1996), score=5.0
		------------------------

##Löschen temporärer Daten

Mahout-Aufträge entfernen keine temporären Daten, die bei der Verarbeitung des Auftrags erstellt wurden. Der `--tempDir`-Parameter wird im Beispielauftrag festgelegt, sodass die temporären Dateien zum einfachen Löschen in einem spezifischen Pfad isoliert abgelegt werden. Verwenden Sie zum Entfernen temporärer Dateien den folgenden Befehl:

	hdfs dfs -rm -f -r /temp/mahouttemp

> [AZURE.WARNING] Wenn Sie den Befehl erneut ausführen möchten, müssen Sie auch das Ausgabeverzeichnis löschen. Verwenden Sie zum Löschen des Verzeichnisses Folgendes:
>
> ```hdfs dfs -rm -f -r /example/data/mahoutout```

## Nächste Schritte

Nachdem Sie sich mit Mahout vertraut gemacht haben, können Sie sich anderen Methoden der Datenverarbeitung in HDInsight zuwenden:

* [Hive mit HDInsight](hdinsight-use-hive.md)
* [Pig mit HDInsight](hdinsight-use-pig.md)
* [MapReduce mit HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
 

<!---HONumber=AcomDC_0914_2016-->