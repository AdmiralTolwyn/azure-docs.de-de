<properties
	pageTitle="Azure-Ressourcen-Manager-Richtlinien | Microsoft Azure"
	description="Beschreibt die Verwendung von Azure-Ressourcen-Manager-Richtlinien, um Verstöße in unterschiedlichen Gültigkeitsbereichen wie Abonnements, Ressourcengruppen oder einzelnen Ressourcen zu verhindern."
	services="azure-resource-manager"
	documentationCenter="na"
	authors="ravbhatnagar"
	manager="ryjones"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="07/12/2016"
	ms.author="gauravbh;tomfitz"/>

# Verwenden von Richtlinien für Ressourcenverwaltung und Zugriffssteuerung

Sie können nun den Azure-Ressourcen-Manager zum Steuern des Zugriffs über benutzerdefinierte Richtlinien verwenden. Mithilfe von Richtlinien können Sie Benutzer in Ihrer Organisation hindern, gegen Konventionen zu verstoßen, die für das Verwalten der Ressourcen Ihrer Organisation gelten.

Sie erstellen Richtliniendefinitionen, die die Aktionen oder Ressourcen beschreiben, die spezifisch verweigert werden. Sie weisen diese Richtliniendefinitionen dem gewünschten Ziel zu, z. B. einem Abonnement, einer Ressourcengruppe oder einer einzelnen Ressource.

In diesem Artikel wird die grundlegende Struktur der Richtliniendefinitionssprache erläutert, mit der Sie Richtlinien erstellen. Anschließend wird beschrieben, wie Sie diese Richtlinien auf verschiedene Ziele anwenden. Zum Schluss finden Sie einige Beispiele dafür, wie Sie dies auch über die REST-API erreichen können.

## Worin unterscheidet sich dies von der rollenbasierten Zugriffssteuerung (RBAC)?

Es gibt einige wichtige Unterschiede zwischen Richtlinien und rollenbasierter Zugriffssteuerung. Als Erstes müssen Sie jedoch verstehen, dass Richtlinien und RBAC zusammenarbeiten. Um Richtlinien verwenden zu können, muss der Benutzer über die RBAC authentifiziert werden. Im Gegensatz zur RBAC stellen Richtlinien ein Standardsystem für das Zulassen und explizite Verweigern dar.

Die RBAC konzentriert sich auf die Aktionen, die ein **Benutzer** in verschiedenen Bereichen ausführen darf. Beispiel: Ein bestimmter Benutzer wird der Rolle „Mitwirkender“ für eine Ressourcengruppe in einem bestimmten Bereich hinzugefügt, sodass der Benutzer Änderungen an der Ressourcengruppe vornehmen darf.

Richtlinien konzentrieren sich auf Aktionen für **Ressourcen** in verschiedenen Bereichen. Beispielsweise können Sie über Richtlinien die Arten von Ressourcen steuern, die bereitgestellt werden können, oder die Standorte beschränken, an denen die Ressourcen bereitgestellt werden können.

## Häufige Szenarios

Ein allgemeines Szenario ist der Bedarf an Tags auf Abteilungsebene zum Zweck der verbrauchsbasierten Kostenzuteilung. Eine Organisation könnte beispielsweise Vorgänge nur zulassen, wenn die richtige Kostenstelle zugeordnet ist, andernfalls soll die Anfrage abgelehnt werden. Dadurch ist es möglich, die Abrechnung für die durchgeführten Vorgänge mit der richtigen Kostenstelle durchzuführen.

In einem weiteren gängigen Szenario möchte die Organisation die Standorte steuern, an denen Ressourcen erstellt werden. Oder sie möchte möglicherweise den Zugriff auf Ressourcen steuern, indem nur bestimmte Arten von Ressourcen bereitgestellt werden dürfen.

Auf ähnliche Weise kann eine Organisation den Dienstkatalog steuern oder die gewünschten Benennungskonventionen für Ressourcen erzwingen.

Mithilfe von Richtlinien können diese Szenarios so einfach wie unten beschrieben umgesetzt werden.

## Struktur von Richtliniendefinitionen

Richtliniendefinitionen werden mit JSON erstellt. Sie enthalten eine oder mehrere Bedingungen/logische Operatoren, die Aktionen und deren Auswirkungen definieren und damit festlegen, was geschieht, wenn die Bedingungen erfüllt sind. Das Schema wird unter [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json) veröffentlicht.

Grundsätzlich enthält eine Richtlinie Folgendes:

**Bedingung/logische Operatoren**: eine Reihe von Bedingungen, die über einen Satz von logischen Operatoren beeinflusst werden.

**Effekt**: beschreibt die Auswirkungen, wenn die Bedingung erfüllt ist – verweigern oder überwachen. Ein Überwachungseffekt gibt ein Warnereignis im Dienstprotokoll aus. Administratoren können z. B. eine Richtlinie erstellen, die eine Überwachung verursacht, wenn jemand einen großen virtuellen Computer erstellt, und dann später die Protokolle überprüfen.

    {
      "if" : {
          <condition> | <logical operator>
      },
      "then" : {
          "effect" : "deny | audit | append"
      }
    }
    
## Richtlinienauswertung

Richtlinien werden ausgewertet, wenn die Erstellung von Ressourcen oder die Bereitstellung von Vorlagen mit HTTP PUT erfolgt. Bei der Bereitstellung von Vorlagen werden Richtlinien ausgewertet, wenn die einzelnen Ressourcen in einer Vorlage erstellt werden.

> [AZURE.NOTE] Die Richtlinie wertet derzeit keine Ressourcentypen aus, die „tags“, „kind“ und „location“ nicht unterstützen, wie etwa der Ressourcentyp „Microsoft.Resources/deployments“. Diese Unterstützung wird zu einem späteren Zeitpunkt hinzugefügt. Um Probleme mit der Abwärtskompatibilität zu vermeiden, sollten Sie beim Erstellen von Richtlinien den Typ explizit angeben. Beispiel: Eine Richtlinie für Tags, in der keine Typen angegeben sind, wird auf alle Typen angewendet. In diesem Fall kann eine Vorlagenbereitstellung in der Zukunft fehlschlagen, wenn eine geschachtelte Ressource vorhanden ist, die „Tag“ nicht unterstützt, und der Ressourcentyp der Bereitstellung der Richtlinienauswertung hinzugefügt wurde.

## Logische Operatoren

Die unterstützten logischen Operatoren sind zusammen mit der Syntax nachfolgend aufgeführt:

| Name des Operators | Syntax |
| :------------- | :------------- |
| Not | "not" : {&lt;Bedingung oder Operator &gt;} |
| Und | "allOf" : [ {&lt;Bedingung oder Operator &gt;},{&lt;Bedingung oder Operator &gt;}] |
| Oder | "anyOf" : [ {&lt;Bedingung oder Operator &gt;},{&lt;Bedingung oder Operator &gt;}] |

Mit dem Ressourcen-Manager können Sie über geschachtelte Operatoren eine komplexe Logik in Ihrer Richtlinie angeben. Sie können z. B. das Erstellen von Ressourcen an einem bestimmten Speicherort für einen angegebenen Ressourcentyp verweigern. Ein Beispiel für geschachtelte Operatoren ist unten dargestellt.

## Bedingungen

Eine Bedingung prüft, ob ein **Feld** oder eine **Quelle** bestimmte Kriterien erfüllt. Die Namen unterstützter Bedingungen sind zusammen mit der Syntax nachstehend aufgeführt:

| Name der Bedingung | Syntax |
| :------------- | :------------- |
| Equals | "equals" : "&lt;Wert&gt;" |
| Like | "like" : "&lt;Wert&gt;" |
| Contains | "contains" : "&lt;Wert&gt;"|
| Geben Sie in | "in" : [ "&lt;Wert1&gt;","&lt;Wert2&gt;" ]|
| ContainsKey | "containsKey" : "&lt;Schlüsselname&gt;" |
| Exists | "exists" : "&lt;bool&gt;" |

### Felder

Bedingungen werden mithilfe von Feldern und Quellen gebildet. Ein Feld stellt Eigenschaften in der Anforderungsnutzlast einer Ressource dar, mit der der Zustand der Ressource beschrieben wird. Eine Quelle stellt Merkmale der Anforderung selbst dar.

Die folgenden Felder und Quellen werden unterstützt:

Felder: **name**, **kind**, **type**, **location**, **tags**, **tags.*** und **property alias**.

### Eigenschaftenaliase 
„property alias“ ist ein Name, der in einer Richtliniendefinition für den Zugriff auf die für einen Ressourcentyp spezifischen Eigenschaften (z. B. Einstellungen und SKUs) verwendet werden kann. Er kann in allen API-Versionen verwendet werden, in denen die Eigenschaft vorhanden ist. Aliase können mithilfe der folgenden REST-API abgerufen werden (die PowerShell-Unterstützung wird zu einem späteren Zeitpunkt hinzugefügt):

    GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01
	
Die Definition eines Alias ist im Folgenden dargestellt. Wie Sie sehen können, definiert ein Alias Pfade in verschiedenen API-Versionen, auch wenn sich der Name einer Eigenschaft ändert.

	"aliases": [
	    {
	      "name": "Microsoft.Storage/storageAccounts/sku.name",
	      "paths": [
	        {
	          "path": "properties.accountType",
	          "apiVersions": [
	            "2015-06-15",
	            "2015-05-01-preview"
	          ]
	        },
	        {
	          "path": "sku.name",
	          "apiVersions": [
	            "2016-01-01"
	          ]
	        }
	      ]
	    }
	]

Derzeit werden die folgenden Aliase unterstützt:

| Aliasname | Beschreibung |
| ---------- | ----------- |
| {resourceType}/sku.name | Unterstützte Ressourcentypen: Microsoft.Compute/virtualMachines,<br />Microsoft.Storage/storageAccounts,<br />Microsoft.Web/serverFarms,<br /> Microsoft.Scheduler/jobcollections,<br />Microsoft.DocumentDB/databaseAccounts,<br />Microsoft.Cache/Redis,<br />Microsoft..CDN/profiles |
| {resourceType}/sku.family | Unterstützter Ressourcentyp: Microsoft.Cache/Redis |
| {resourceType}/sku.capacity | Unterstützter Ressourcentyp: Microsoft.Cache/Redis |
| Microsoft.Compute/virtualMachines/imagePublisher | |
| Microsoft.Compute/virtualMachines/imageOffer | |
| Microsoft.Compute/virtualMachines/imageSku | |
| Microsoft.Compute/virtualMachines/imageVersion | |
| Microsoft.Cache/Redis/enableNonSslPort | |
| Microsoft.Cache/Redis/shardCount | |
| Microsoft.SQL/servers/version | |
| Microsoft.SQL/servers/databases/requestedServiceObjectiveId | |
| Microsoft.SQL/servers/databases/requestedServiceObjectiveName | |
| Microsoft.SQL/servers/databases/edition | |
| Microsoft.SQL/servers/databases/elasticPoolName | |
| Microsoft.SQL/servers/elasticPools/dtu | |
| Microsoft.SQL/servers/elasticPools/edition | |

Derzeit gilt die Richtlinie nur bei PUT-Anforderungen.

## Effekt
Die Richtlinie unterstützt drei Arten von Auswirkungen: **deny**, **audit** und **append**.

- Mit „deny“ wird ein Ereignis im Überwachungsprotokoll generiert, und die Anforderung schlägt fehl.
- Mit „audit“ wird ein Ereignis im Überwachungsprotokoll generiert, aber die Anforderung schlägt nicht fehl.
- Mit „append“ wird der Anforderung der definierte Satz von Feldern hinzugefügt.

Für **append** müssen Sie die unten dargestellten Details angeben:

    ....
    "effect": "append",
    "details": [
      {
        "field": "field name",
        "value": "value of the field"
      }
    ]

Der Wert kann entweder eine Zeichenfolge oder ein Objekt im JSON-Format sein.

## Beispiele für Richtliniendefinitionen

Im Folgenden erfahren Sie, wie Sie die Richtlinie für die oben genannten Szenarios definieren.

### Verbrauchsbasierte Kostenzuteilung: Anfordern von Abteilungstags

Die Richtlinie unten verweigert alle Anforderungen, die kein Tag mit dem Schlüssel "costCenter" enthalten.

    {
      "if": {
        "not" : {
          "field" : "tags",
          "containsKey" : "costCenter"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

Die Richtlinie unten fügt ein costCenter-Tag mit einem vordefinierten Wert an, wenn keine Tags vorhanden sind.

	{
	  "if": {
	    "field": "tags",
	    "exists": "false"
	  },
	  "then": {
	    "effect": "append",
	    "details": [
	      {
	        "field": "tags",
	        "value": {"costCenter":"myDepartment" }
	      }
	    ]
	  }
	}
	
Die Richtlinie unten fügt ein costCenter-Tag mit einem vordefinierten Wert an, wenn andere Tags vorhanden sind.

	{
	  "if": {
	    "allOf": [
	      {
	        "field": "tags",
	        "exists": "true"
	      },
	      {
	        "field": "tags.costCenter",
	        "exists": "false"
	      }
	    ]
	
	  },
	  "then": {
	    "effect": "append",
	    "details": [
	      {
	        "field": "tags.costCenter",
	        "value": "myDepartment"
	      }
	    ]
	  }
	}


### Geografische Compliance: Erzwingen von Ressourcenpfaden

Das folgende Beispiel zeigt eine Richtlinie, die alle Anfragen ablehnt, deren Standort nicht Nordeuropa oder Westeuropa ist.

    {
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### Dienstverwaltung: Auswählen des Dienstkatalogs

Das folgende Beispiel veranschaulicht die Verwendung der Quelle. Es zeigt, dass Aktionen nur für Dienste des Typs "Microsoft.Resources/*", "Microsoft.Compute/*", "Microsoft.Storage/*" und "Microsoft.Network/*" zulässig sind. Alle anderen Anforderungen werden verweigert.

    {
      "if" : {
        "not" : {
          "anyOf" : [
            {
              "field" : "type",
              "like" : "Microsoft.Resources/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Compute/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Storage/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Network/*"
            }
          ]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### Verwenden von genehmigten SKUs

Im folgenden Beispiel wird die Verwendung von „property alias“ zum Einschränken von SKUs veranschaulicht. In diesem Beispiel sind nur „Standard\_LRS“ und „Standard\_GRS“ zur Verwendung für Speicherkonten genehmigt.

    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "allof": [
                {
                  "field": "Microsoft.Storage/storageAccounts/sku.name",
                  "in": ["Standard_LRS", "Standard_GRS"]
                }
              ]
            }
          }
        ]
      },
      "then": {
        "effect": "deny"
      }
    }
    

### Benennungskonvention

Das folgende Beispiel veranschaulicht die Verwendung von Platzhalterzeichen, die von der Bedingung "like" unterstützt werden. Die Bedingung gibt an, dass die Anforderung verweigert wird, wenn der Name nicht mit dem angegebenen Muster (Namenspräfix*Namenssuffix) übereinstimmt.

    {
      "if" : {
        "not" : {
          "field" : "name",
          "like" : "namePrefix*nameSuffix"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }
    
### Taganforderung nur für Speicherressourcen

Das folgende Beispiel zeigt, wie logische Operatoren so verschachtelt werden, dass ein Anwendungstag nur für Speicherressourcen benötigt wird.

    {
        "if": {
            "allOf": [
              {
                "not": {
                  "field": "tags",
                  "containsKey": "application"
                }
              },
              {
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
              }
            ]
        },
        "then": {
            "effect": "audit"
        }
    }

## Richtlinienzuweisung

Richtlinien können auf verschiedene Gültigkeitsbereiche wie Abonnements, Ressourcengruppen und einzelne Ressourcen angewendet werden. Richtlinien werden von allen untergeordneten Ressourcen geerbt. Wenn also eine Richtlinie auf eine Ressourcengruppe angewendet wird, gilt sie auch für alle Ressourcen in der Ressourcengruppe.

## Erstellen einer Richtlinie

Dieser Abschnitt enthält Informationen zum Erstellen einer Richtlinie mithilfe der REST-API.

### Erstellen der Richtliniendefinition mit der REST-API

Sie können eine Richtlinie mit der REST-API erstellen, wie unter [Policy Definitions](https://msdn.microsoft.com/library/azure/mt588471.aspx) (in englischer Sprache) beschrieben. Die REST-API ermöglicht es Ihnen, Richtliniendefinitionen zu erstellen und zu löschen sowie Informationen zu vorhandenen Definitionen abzurufen.

So erstellen Sie eine neue Richtlinie

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

Der Anforderungstext sollte dem folgenden ähneln:

    {
      "properties":{
        "policyType":"Custom",
        "description":"Test Policy",
        "policyRule":{
          "if" : {
            "not" : {
              "field" : "tags",
              "containsKey" : "costCenter"
            }
          },
          "then" : {
            "effect" : "deny"
          }
        }
      },
      "name":"testdefinition"
    }


Die Richtliniendefinition kann als eines der oben aufgeführten Beispiele definiert werden. Verwenden Sie als „api-version“ die Einstellung *2016-04-01*. Weitere Beispiele und Informationen finden Sie unter [Policy Definitions](https://msdn.microsoft.com/library/azure/mt588471.aspx) (in englischer Sprache).

### Erstellen der Richtliniendefinition mit der PowerShell

Sie können eine neue Richtliniendefinition mithilfe des Cmdlets "New-AzureRmPolicyDefinition" erstellen, wie unten gezeigt. Das unten angeführte Beispiel erstellt eine Richtlinie, um Ressourcen nur in Nordeuropa und Westeuropa zuzulassen.

    $policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy '{	
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
    	}
      },
      "then" : {
        "effect" : "deny"
      }
    }'    		

Die Ausgabe der Ausführung wird im $policy-Objekt gespeichert und kann später während der Richtlinienzuweisung verwendet werden. Als Richtlinienparameter kann auch der Pfad zu einer JSON-Datei, die die Richtlinie enthält, angegeben werden, anstatt die Richtlinie inline anzugeben, wie unten gezeigt.

    New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain 	regions" -Policy "path-to-policy-json-on-disk"


## Anwenden einer Richtlinie

### Zuweisen von Richtlinien mit der REST-API

Sie können die Richtliniendefinition mithilfe von [Policy Assignments](https://msdn.microsoft.com/library/azure/mt588466.aspx) (Richtlinienzuweisungen, in englischer Sprache) auf den gewünschten Bereich anwenden. Die REST-API ermöglicht es Ihnen, Richtlinienzuweisungen zu erstellen und zu löschen sowie Informationen zu vorhandenen Zuweisungen abzurufen.

Führen Sie zum Erstellen einer neuen Richtlinienzuweisung Folgendes aus:

    PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}

"{policy-assignment}" ist der Name der Richtlinienzuweisung. Verwenden Sie als „api-version“ die Einstellung *2016-04-01*.

Der Anforderungstext sollte dem folgenden ähneln:

    {
      "properties":{
        "displayName":"VM_Policy_Assignment",
        "policyDefinitionId":"/subscriptions/########/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
        "scope":"/subscriptions/########-####-####-####-############"
      },
      "name":"VMPolicyAssignment"
    }

Weitere Beispiele und Informationen finden Sie unter [Policy Assignments](https://msdn.microsoft.com/library/azure/mt588466.aspx) (Richtlinienzuweisungen, in englischer Sprache).

### Zuweisen von Richtlinien mit der PowerShell

Sie können die oben erstellte Richtlinie mithilfe der PowerShell für den gewünschten Bereich erstellen, indem Sie das Cmdlet "New-AzureRmPolicyAssignment" verwenden, wie unten gezeigt:

    New-AzureRmPolicyAssignment -Name regionPolicyAssignment -PolicyDefinition $policy -Scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>
        
Hierbei ist "$policy" das Richtlinienobjekt, das als Ergebnis der Ausführung des Cmdlets "New-AzureRmPolicyDefinition" zurückgegeben wurde, wie oben gezeigt. Der Bereich ist in diesem Fall der Name der von Ihnen angegebenen Ressourcengruppe.

Wenn Sie die oben genannte Richtlinienzuweisung entfernen möchten, können Sie hierzu wie folgt vorgehen:

    Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Sie können Richtliniendefinitionen mithilfe der Cmdlets "Get-AzureRmPolicyDefinition", "Set-AzureRmPolicyDefinition" bzw. "Remove-AzureRmPolicyDefinition" abrufen, ändern oder entfernen.

Ähnlich können Sie Richtlinienzuweisungen mithilfe der Cmdlets "Get-AzureRmPolicyAssignment", "Set-AzureRmPolicyAssignment" bzw. "Remove-AzureRmPolicyAssignment" abrufen, ändern oder entfernen.

##Richtlinie zur Überwachung von Ereignissen

Nachdem Sie die Richtlinie angewendet haben, werden Sie richtlinienbezogene Ereignisse sehen können. Sie können entweder zum Portal wechseln oder PowerShell verwenden, um diese Daten abzurufen.

Zum Anzeigen aller Ereignisse, die mit dem Verweigerungseffekt in Verbindung stehen, können Sie den folgenden Befehl verwenden.

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/deny/action"} 

Zum Anzeigen aller Ereignisse, die mit dem Überwachungseffekt in Verbindung stehen, können Sie den folgenden Befehl verwenden.

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/audit/action"} 
    

<!---HONumber=AcomDC_0810_2016-->