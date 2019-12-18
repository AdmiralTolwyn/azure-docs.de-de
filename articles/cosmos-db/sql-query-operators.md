---
title: SQL-Abfrageoperatoren für Azure Cosmos DB
description: Erfahren Sie mehr über SQL-Operatoren wie Gleichheit, Vergleich und logische Operatoren, die von Azure Cosmos DB unterstützt werden.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: f3efe4bee749f0d3132206ca68a33a60f0e16b81
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870937"
---
# <a name="operators-in-azure-cosmos-db"></a>Operatoren in Azure Cosmos DB

In diesem Artikel werden die verschiedenen Operatoren beschrieben, die von Azure Cosmos DB unterstützt werden.

## <a name="equality-and-comparison-operators"></a>Gleichheits- und Vergleichsoperatoren

Die folgende Tabelle zeigt die Ergebnisse für Gleichheitsvergleiche in der SQL-API zwischen den einzelnen JSON-Typen.

| **Op** | **Undefiniert** | **NULL** | **Boolescher Wert** | **Number** | **String** | **Object** | **Array** |
|---|---|---|---|---|---|---|---|
| **Undefiniert** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined |
| **NULL** | Undefined | **OK** | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Boolescher Wert** | Undefined | Undefined | **OK** | Undefined | Undefined | Undefined | Undefined |
| **Number** | Undefined | Undefined | Undefined | **OK** | Undefined | Undefined | Undefined |
| **String** | Undefined | Undefined | Undefined | Undefined | **OK** | Undefined | Undefined |
| **Object** | Undefined | Undefined | Undefined | Undefined | Undefined | **OK** | Undefined |
| **Array** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | **OK** |

Bei Vergleichsoperatoren wie `>`, `>=`, `!=`, `<` und `<=` ergeben Vergleiche unterschiedlicher Typen oder zwischen zwei Objekten oder Arrays den Wert `Undefined`.  

Wenn das Ergebnis des skalaren Ausdrucks `Undefined` ist, wird das Element nicht in das Ergebnis eingeschlossen, da `Undefined` nicht gleich `true` ist.

## <a name="logical-and-or-and-not-operators"></a>Logische Operatoren (AND, OR und NOT)

Logische Operatoren arbeiten mit booleschen Werten. Es folgt eine Liste der logischen Wahrheitstabellen für diese Operatoren:

**OR-Operator**

| OR | True | False | Undefined |
| --- | --- | --- | --- |
| True |True |True |True |
| False |True |False |Undefined |
| Undefined |True |Undefined |Undefined |

**AND-Operator**

| AND | True | False | Undefined |
| --- | --- | --- | --- |
| True |True |False |Undefined |
| False |False |False |False |
| Undefined |Undefined |False |Undefined |

**NOT-Operator**

| NOT |  |
| --- | --- |
| True |False |
| False |True |
| Undefined |Undefined |


## <a name="-operator"></a>*-Operator

Mit dem Sonderoperator * wird das gesamte Element unverändert projiziert. Wenn dieser Operator verwendet wird, dürfen keine weiteren projizierten Felder existieren. Eine Abfrage wie `SELECT * FROM Families f` ist gültig, aber `SELECT VALUE * FROM Families f` und `SELECT *, f.id FROM Families f` sind nicht gültig.

## <a name="-and--operators"></a>? und ?? – Operatoren

Sie können ternäre (?) und koaleszierte (??) Operatoren wie in den Programmiersprachen C# und JavaScript zum Erstellen von bedingten Ausdrücken verwenden. 

Mit dem Operator ? können Sie JSON-Eigenschaften Ad hoc erstellen. Beispielsweise werden mit der folgenden Abfrage die Klassenstufen in `elementary` oder `other` klassifiziert:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Sie können auch Aufrufe des Operators ? wie in der folgenden Abfrage verschachteln: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Wie bei anderen Abfrageoperatoren schließt der Operator ? Elemente aus, wenn die referenzierten Eigenschaften fehlen oder unterschiedliche Typen miteinander verglichen werden.

Der Operator ?? kann bei Abfragen für teilweise strukturiert oder gemischte Datentypen zur effizienten Prüfung des Vorkommens einer Eigenschaft in einem Element verwendet werden. Die folgende Abfrage gibt z.B. `lastName` zurück, falls vorhanden, oder `surname` zurück, falls `lastName` nicht vorhanden ist.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Nächste Schritte

- [Azure Cosmos DB-.NET-Beispiele](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Schlüsselwörter](sql-query-keywords.md)
- [SELECT-Klausel](sql-query-select.md)
