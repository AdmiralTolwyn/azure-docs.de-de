---
title: Bereitstellen des Blaupausenbeispiels „CIS Microsoft Azure Foundations Benchmark“
description: Bereitstellungsschritte für das Blaupausenbeispiel „CIS Microsoft Azure Foundations Benchmark“, einschließlich Details zum Blaupausenartefakt-Parameter.
ms.date: 10/14/2019
ms.topic: sample
ms.openlocfilehash: 7476addee3d31ec355c793ab7fbe1fbb732b8013
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2020
ms.locfileid: "74544522"
---
# <a name="deploy-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>Bereitstellen des Blaupausenbeispiels „CIS Microsoft Azure Foundations Benchmark“

Zum Bereitstellen des Blaupausenbeispiels „CIS Microsoft Azure Foundations Benchmark“ von Azure Blueprints müssen die folgenden Schritte ausgeführt werden:

> [!div class="checklist"]
> - Erstellen einer neuen Blaupause mithilfe des Beispiels
> - Markieren Ihrer Kopie des Beispiels als **Veröffentlicht**
> - Zuweisen Ihrer Kopie der Blaupause zu einem vorhandenen Abonnement

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen, bevor Sie beginnen.

## <a name="create-blueprint-from-sample"></a>Erstellen einer Blaupause mithilfe des Beispiels

Implementieren Sie zuerst das Blaupausenbeispiel, indem Sie mithilfe des Beispiels eine neue Blaupause in Ihrer Umgebung erstellen.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Klicken Sie links auf der Seite **Erste Schritte** unter _Blaupause erstellen_ auf die Schaltfläche **Erstellen**.

1. Navigieren Sie unter _Weitere Beispiele_ zum Blaupausenbeispiel **CIS Microsoft Azure Foundations Benchmark v1.1.0**, und wählen Sie **Dieses Beispiel verwenden** aus.

1. Geben Sie die _Grundlagen_ des Blaupausenbeispiels ein:

   - **Name der Blaupause**: Geben Sie einen Namen für Ihre Kopie des Blaupausenbeispiels „CIS Microsoft Azure Foundations Benchmark“ an.
   - **Definitionsspeicherort**: Klicken Sie auf die Schaltfläche mit den Auslassungspunkten, und wählen Sie die Verwaltungsgruppe aus, in der Sie Ihre Kopie des Beispiels speichern möchten.

1. Wählen Sie oben auf der Seite die Registerkarte _Artefakte_ oder unten auf der Seite die Option **Weiter: Artefakte** aus.

1. Überprüfen Sie die Liste der Artefakte, aus denen das Blaupausenbeispiel besteht. Viele der Artefakte enthalten Parameter, die Sie später definieren. Wählen Sie **Entwurf speichern** aus, nachdem Sie das Blaupausenbeispiel überprüft haben.

## <a name="publish-the-sample-copy"></a>Veröffentlichen der Kopie des Beispiels

Ihre Kopie des Blaupausenbeispiels wurde jetzt in Ihrer Umgebung erstellt. Sie wird im Modus **Entwurf** erstellt und muss **veröffentlicht** werden, bevor sie zugewiesen und bereitgestellt werden kann. Die Kopie des Blaupausenbeispiels kann Ihrer Umgebung und Ihren Anforderungen angepasst werden. Durch diese Änderungen ist sie aber möglicherweise nicht mehr mit den Empfehlungen zu „CIS Microsoft Azure Foundations Benchmark“ konform.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Wählen Sie links die Seite **Blaupausendefinitionen** aus. Verwenden Sie die Filter, um Ihre Kopie des Blaupausenbeispiels zu suchen, und wählen Sie es aus.

1. Wählen Sie oben auf der Seite die Option **Blaupause veröffentlichen** aus. Geben Sie auf der neuen Seite rechts für Ihre Kopie des Blaupausenbeispiels eine **Version** an. Diese Eigenschaft ist hilfreich, wenn Sie später Änderungen vornehmen. Geben Sie **Änderungshinweise** an, etwa „Erste vom Blaupausenbeispiel ‚CIS Microsoft Azure Foundations Benchmark‘ veröffentlichte Version“. Wählen Sie dann unten auf der Seite **Veröffentlichen** aus.

## <a name="assign-the-sample-copy"></a>Zuweisen der Kopie des Beispiels

Nachdem die Kopie des Blaupausenbeispiels erfolgreich **veröffentlicht** wurde, kann sie einem Abonnement innerhalb der Verwaltungsgruppe, in der sie gespeichert wurde, zugewiesen werden. In diesem Schritt werden Parameter angegeben, damit jede Bereitstellung der Kopie des Blaupausenbeispiels eindeutig ist.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Wählen Sie links die Seite **Blaupausendefinitionen** aus. Verwenden Sie die Filter, um Ihre Kopie des Blaupausenbeispiels zu suchen, und wählen Sie es aus.

1. Wählen Sie oben auf der Seite mit der Blaupausendefinition die Option **Blaupause zuweisen** aus.

1. Geben Sie die Parameterwerte für die Blaupausenzuweisung an:

   - Grundlagen

     - **Abonnements**: Wählen Sie mindestens eines der Abonnements in der Verwaltungsgruppe aus, in der Sie die Kopie des Blaupausenbeispiels gespeichert haben. Wenn Sie mehrere Abonnements auswählen, wird für jedes Abonnement eine Zuweisung mit den eingegebenen Parametern erstellt.
     - **Zuweisungsname:** Der Name wird basierend auf dem Namen der Blaupause vorab ausgefüllt.
       Sie können ihn nach Bedarf ändern oder unverändert übernehmen.
     - **Standort**: Wählen Sie eine Region aus, in der die verwaltete Identität erstellt werden soll. Azure Blueprint verwendet diese verwaltete Identität zum Bereitstellen aller Artefakte in der zugewiesenen Blaupause. Weitere Informationen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Version der Blaupausendefinition:** Wählen Sie eine **veröffentlichte** Version Ihrer Kopie des Blaupausenbeispiels aus.

   - Zuweisung sperren

     Wählen Sie die Blaupausensperreinstellung für die Umgebung aus. Weitere Informationen finden Sie unter [Grundlegendes zur Ressourcensperre in Azure Blueprint](../../concepts/resource-locking.md).

   - Verwaltete Identität

     Behalten Sie die Standardeinstellung _Vom System zugewiesen_ für die verwaltete Identität bei.

   - Artefaktparameter

     Die in diesem Abschnitt definierten Parameter gelten für das Artefakt, unter dem sie definiert werden. Diese Parameter sind [dynamische Parameter](../../concepts/parameters.md#dynamic-parameters), da sie während der Zuweisung der Blaupause definiert werden. Eine vollständige Liste der Artefaktparameter und die zugehörigen Beschreibungen finden Sie in der [Tabelle der Artefaktparameter](#artifact-parameters-table).

1. Nachdem Sie alle Parameter eingegeben haben, wählen Sie unten auf der Seite die Option **Zuweisen** aus. Die Blaupausenzuweisung wird erstellt, und die Artefaktbereitstellung wird gestartet. Die Bereitstellung dauert ungefähr eine Stunde. Um den Status der Bereitstellung zu überprüfen, öffnen Sie die Blaupausenzuweisung.

> [!WARNING]
> Der Azure Blueprints-Dienst und die integrierten Blaupausenbeispiele sind **kostenlos**. Azure-Ressourcen werden [nach Produkt abgerechnet](https://azure.microsoft.com/pricing/). Verwenden Sie den [Preisrechner](https://azure.microsoft.com/pricing/calculator/) zum Schätzen der Kosten für die Ausführung der Ressourcen, die mit diesem Blaupausenbeispiel bereitgestellt werden.

## <a name="artifact-parameters-table"></a>Tabelle der Artefaktparameter

Die folgende Tabelle enthält eine Aufstellung der Parameter des Blaupausenartefakts:

|Artefaktname|Artefakttyp|Parametername|BESCHREIBUNG|
|-|-|-|-|
|\[Vorschau\]: CIS Microsoft Azure Foundations Benchmark 1.1.0-Empfehlungen überwachen und spezifische unterstützende VM-Erweiterungen bereitstellen|Richtlinienzuweisung|Liste der Regionen, in denen Network Watcher aktiviert werden soll|Eine durch Semikolons getrennte Liste von Regionen. Verwenden Sie Get-AzLocation, um eine vollständige Liste der Regionen abzurufen. Beispiel: eastus; eastus2|
|\[Vorschau\]: CIS Microsoft Azure Foundations Benchmark 1.1.0-Empfehlungen überwachen und spezifische unterstützende VM-Erweiterungen bereitstellen|Richtlinienzuweisung|Liste der zur Verwendung genehmigten VM-Erweiterungen|Eine durch Semikolons getrennte Liste von Erweiterungen. Verwenden Sie Get-AzVMExtensionImage, um eine vollständige Liste der VM-Erweiterungen anzuzeigen. Beispiel: AzureDiskEncryption; IaaSAntimalware|

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich die Schritte zur Bereitstellung des Blaupausenbeispiels „CIS Microsoft Azure Foundations Benchmark“ angesehen haben, lesen Sie nun die folgenden Artikel, um mehr über die Blaupausen- und Steuerungszuordnung zu erfahren:

> [!div class="nextstepaction"]
> [Übersicht zum Blaupausenbeispiel „CIS Microsoft Azure Foundations Benchmark“](./index.md)
> [Blaupausenbeispiel „CIS Microsoft Azure Foundations Benchmark“: Steuerungszuordnung](./control-mapping.md)

Weitere Artikel zu Blaupausen und ihrer Nutzung:

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](../../concepts/lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](../../concepts/parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](../../concepts/sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](../../concepts/resource-locking.md) können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../../how-to/update-existing-assignments.md).
