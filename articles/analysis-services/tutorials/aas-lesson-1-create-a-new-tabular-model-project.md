---
title: 'Azure Analysis Services-Tutorial, Lektion 1: Erstellen eines neuen Projekts mit tabellarischem Modell | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt, wie ein neues Azure Analysis Services-Tutorialprojekt erstellt werden kann.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3291721847d34b0fa9a6259bfeb6ec6fa06ed2b5
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54188010"
---
# <a name="create-a-tabular-model-project"></a>Erstellen eines tabellarischen Modellprojekts

In dieser Lektion verwenden Sie Visual Studio mit Analysis Services-Projekten oder SQL Server Data Tools (SSDT), um ein neues tabellarisches Modellprojekt mit dem Kompatibilitätsgrad 1400 zu erstellen. Sobald das neue Projekt erstellt wurde, können Sie damit beginnen, Ihr Modell zu erstellen und Daten hinzuzufügen. In dieser Lektion erhalten Sie eine kurze Einführung in die tabellarische Modellerstellungsumgebung in Visual Studio.  
  
Geschätzte Zeit zum Bearbeiten dieser Lektion: **10 Minuten**  
  
## <a name="prerequisites"></a>Voraussetzungen  
Dieses Thema ist die erste Lektion in einem Tutorial über tabellarische Modellerstellung. Es müssen einige Voraussetzungen erfüllt sein, um diese Lektion abschließen zu können. Weitere Informationen erhalten Sie in [Azure Analysis Services – Tutorial von Adventure Works](../tutorials/aas-adventure-works-tutorial.md).  
  
## <a name="create-a-new-tabular-model-project"></a>Erstellen eines neuen tabellarischen Modellprojekts  
  
#### <a name="to-create-a-new-tabular-model-project"></a>Erstellen eines neuen tabellarischen Modellprojekts  
  
1.  Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.  
  
2.  Erweitern Sie im Dialogfeld **Neues Projekt** Dialogfeld **installierte** > **Business- Intelligence-** > **Analysedienste**, und klicken Sie dann auf **tabellarisches Analysis Services-Projekts**.  
  
3.  Geben Sie als **Namen** **AW-Internetverkäufe** ein, und geben Sie dann einen Speicherort für die Projektdateien an.  
  
    Standardmäßig entspricht der **Projektmappenname** dem Projektnamen; Sie können aber einen anderen Projektmappennamen eingeben.  
  
4.  Klicken Sie auf **OK**.  
  
5.  Im **Designer für tabellarische Modelle** wählen Sie im Dialogfeld **Integrierter Arbeitsbereich** aus.  
  
    Der Arbeitsbereich umfasst eine tabellarische Modelldatenbank, die denselben Namen trägt wie das Projekt während der Modellerstellung. Integrierter Arbeitsbereich bedeutet, dass Visual Studio eine integrierte Instanz verwendet und deswegen kein separater Analysis Services-Server installiert werden muss, der nur für die Modellerstellung bestimmt ist.
      
6.  Wählen Sie in der Option **Kompatibilitätsgrad** **SQL Server 2017 / Azure Analysis Services (1400)**.   
 
    ![aas-lesson1-tmd](../tutorials/media/aas-lesson1-tmd.png)
      
    Wenn SQL Server 2017 / Azure Analysis Services (1400) nicht im Kompatibilitätsgrad-Listenfeld angezeigt wird, verwenden Sie nicht die neueste Version der SQL Server-Datentools. Um die neueste Version zu erhalten, siehe [Installieren der SQL Server-Datentools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  
      
  
## <a name="understanding-the-visual-studio-tabular-model-authoring-environment"></a>Grundlagen der Umgebung für die tabellarische Modellerstellung von Visual Studio  
Nachdem Sie nun ein neues tabellarisches Modellprojekt erstellt haben, möchten wir uns kurz die tabellarische Modellerstellungsumgebung in Visual Studio ansehen.  
  
Nachdem das Projekt erstellt wurde, wird es in Visual Studio geöffnet. Auf der rechten Seite unter **Tabellarischer Modell-Explorer** sehen Sie eine Strukturansicht der Objekte in Ihrem Modell. Die Ordner sind leer, da Sie noch keine Daten importiert haben. Ähnlich wie bei der Menüleiste können Sie mit der rechten Maustaste auf einen Objektordner klicken. In den verschiedenen Schritten in diesem Tutorial verwenden Sie den tabellarischen Modell-Explorer, um durch die verschiedenen Objekte in Ihrem Modellprojekt zu navigieren.

![aas-lesson1-tme](../tutorials/media/aas-lesson1-tme.png)

Klicken Sie auf die Registerkarte **Projektmappen-Explorer**. Hier wird Ihre Datei **Model.bim** angezeigt. Wenn das Designer-Fenster nicht auf der linken Seite angezeigt wird (das leere Fenster mit der Registerkarte Model.bim), doppelklicken Sie im **Projektmappen-Explorer** unter **AW Internet-Verkaufsprojekt** auf die Datei **Model.bim**. Die Datei „Model.bim“ enthält alle Metadaten für Ihr Modellprojekt. 

![aas-lesson1-se](../tutorials/media/aas-lesson1-se.png)
  
Klicken Sie auf **Model.bim**. Im **Eigenschaftenfenster** werden die Modelleigenschaften angezeigt. Die wichtigste davon ist die Eigenschaft **DirectQuery-Modus**. Diese Eigenschaft gibt an, ob das Modell im In-Memory-Modus (Aus) oder im DirectQuery-Modus (Ein) bereitgestellt wird. Für dieses Tutorial werden Sie Ihr Modell im In-Memory-Modus erstellen und bereitstellen.

![aas-lesson1-properties](../tutorials/media/aas-lesson1-properties.png)
  
Wenn Sie ein neues Modell erstellen, werden bestimmte Modelleigenschaften gemäß den Datenmodellierungseinstellungen automatisch festgelegt. Diese Einstellungen können im Menü **Tools** im Dialogfeld **Optionen** festgelegt werden. Durch die Eigenschaften „Datensicherung“, „Arbeitsbereich beibehalten“ und „Arbeitsbereichsserver“ ist festgelegt, wie und wo die Datenbank des Arbeitsbereichs (Ihre Modellerstellungsdatenbank) gesichert, im Speicher gehalten und erstellt werden soll. Sie können diese Einstellungen später ändern, falls erforderlich, aber vorläufig lassen Sie diese Eigenschaften unverändert.  

Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **AW-Internetverkäufe Sales** (Projekt), und klicken Sie dann auf **Eigenschaften**. Das Dialogfeld **Eigenschaftenseiten der AW-Internetverkäufe** wird angezeigt. Sie legen einige dieser Eigenschaften später fest, wenn Sie das Modell bereitstellen.  
  
Wenn Sie Analysis Services-Projekte oder SSDT installiert haben, wurden der Visual Studio-Umgebung mehrere neue Menüelemente hinzugefügt. Klicken Sie auf das Menü **Modell**. Hier können Sie Daten importieren, die Daten des Arbeitsbereichs aktualisieren, das Modell in Excel analysieren, Perspektiven und Rollen erstellen, die Modellansicht auswählen und Berechnungsoptionen festlegen. Klicken Sie auf das Menü **Tabelle**. Hier können Sie Beziehungen erstellen und verwalten, Einstellungen für Datentabellen festlegen, Partitionen erstellen und die Tabelleneigenschaften bearbeiten. Wenn Sie auf das Menü **Spalte** klicken, können Sie Spalten in einer Tabelle hinzufügen oder diese löschen oder fixieren und die Sortierreihenfolge festlegen. Visual Studio fügt der Leiste auch einige Schaltflächen hinzu. Besonders nützlich ist die Funktion „AutoSumme“, mit der Sie für eine ausgewählte Spalte ein Standardaggregationsmaß erstellen können. Einige andere Schaltflächen der Symbolleiste bieten schnellen Zugriff auf häufig verwendete Funktionen und Befehle.  
  
Erkunden Sie einige der Dialoge und Speicherorte für verschiedene Funktionalitäten zur Erstellung von tabellarischen Modellen. Einige Elemente sind noch nicht aktiv, aber Sie können sich bereits eine gute Vorstellung von einer Erstellungsumgebung für tabellarische Modelle verschaffen.  
  

## <a name="whats-next"></a>Wie geht es weiter?
[Lektion 2: Abrufen von Daten](../tutorials/aas-lesson-2-get-data.md).

  
  
  
