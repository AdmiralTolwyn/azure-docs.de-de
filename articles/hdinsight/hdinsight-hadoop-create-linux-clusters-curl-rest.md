<properties
   	pageTitle="Bereitstellen von Hadoop-, HBase- oder Storm-Clustern unter Linux in HDInsight mit cURL und der Azure-REST-API | Microsoft Azure"
   	description="Erfahren Sie, wie Sie Linux-basierte HDInsight-Cluster mit cURL, Azure-Ressourcen-Manager-Vorlagen und der Azure-REST-API erstellen. Sie können den Cluster-Typ (Hadoop, HBase oder Storm) angeben oder Skripts verwenden, um benutzerdefinierte Komponenten zu installieren."
   	services="hdinsight"
   	documentationCenter=""
   	authors="Blackmist"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="07/27/2016"
   	ms.author="larryfr"/>

#Erstellen von Linux-basierten Clustern in HDInsight mithilfe von cURL und der Azure-REST-API

[AZURE.INCLUDE [Auswahl](../../includes/hdinsight-selector-create-clusters.md)]

Mit der Azure-REST-API können Sie Verwaltungsvorgänge für Dienste durchführen, die auf der Azure-Plattform gehostet werden. Beispielsweise können Sie neue Ressourcen wie etwa Linux-basierte HDInsight-Cluster erstellen. In diesem Dokument erfahren Sie, wie Sie Azure-Ressourcen-Manager-Vorlagen erstellen, um einen HDInsight-Cluster sowie den zugehörigen Speicher zu konfigurieren. Sie erfahren auch, wie Sie anschließend die Vorlage mithilfe von cURL in der Azure-REST-API bereitstellen, um einen neuen HDInsight-Cluster zu erstellen.

> [AZURE.IMPORTANT] Bei den Schritten in diesem Dokument wird die Standardanzahl von Workerknoten (4) für einen HDInsight-Cluster verwendet. Wenn Sie mehr als 32 Workerknoten planen, entweder bei Erstellung des Clusters oder durch eine Skalierung des Clusters nach der Erstellung, müssen Sie eine Hauptknotengröße von mindestens 8 Kernen und 14 GB Arbeitsspeicher (RAM) auswählen.
>
> Weitere Informationen zu Knotengrößen und damit verbundenen Kosten finden Sie unter [HDInsight – Preise](https://azure.microsoft.com/pricing/details/hdinsight/).

##Voraussetzungen

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __Azure-Befehlszeilenschnittstelle__. Die Azure-Befehlszeilenschnittstelle wird verwendet, um einen Dienstprinzipal zu erstellen, der zum Generieren von Authentifizierungstoken für Anforderungen an die Azure-REST-API verwendet wird.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

- __cURL__. Dieses Hilfsprogramm ist über Ihr Paketverwaltungssystem verfügbar oder kann hier heruntergeladen werden: [http://curl.haxx.se/](http://curl.haxx.se/).

    > [AZURE.NOTE] Wenn Sie PowerShell zum Ausführen der Befehle in diesem Dokument verwenden, müssen Sie zuerst den `curl`-Alias entfernen, der standardmäßig erstellt wird. Wenn Sie den `curl`-Befehl von einer PowerShell-Eingabeaufforderung ausführen, verwendet dieser Alias das PowerShell-Cmdlet "Invoke-WebRequest" anstelle von cURL und gibt für viele der in diesem Dokument angegebenen Befehle Fehler zurück.
    > 
    > Um diesen Alias zu entfernen, verwenden Sie folgenden Befehl in der PowerShell-Eingabeaufforderung:
    >
    > `Remove-item alias:curl`
    >
    > Nachdem der Alias entfernt wurde, können Sie die auf Ihrem System installierte cURL-Version verwenden.

##Erstellen einer Vorlage

Bei Azure-Ressourcen-Manager-Vorlagen handelt es sich um JSON-Dokumente, mit denen eine __Ressourcengruppe__ und alle darin enthaltenen Ressourcen (z. B. HDInsight) beschrieben werden. Dieser vorlagenbasierte Ansatz ermöglicht Ihnen das Definieren aller für HDInsight benötigten Ressourcen in einer einzigen Vorlage sowie das Verwalten von Änderungen an der gesamten Gruppe durch __Bereitstellungen__, mit denen Änderungen an der Gruppe vorgenommen werden.

Vorlagen werden üblicherweise in zwei Teilen bereitgestellt: die Vorlage selbst und eine Parameterdatei, die Sie mit Werten auffüllen, die speziell für Ihre Konfiguration gelten. Beispiel: Clustername, Administratorname und -kennwort. Wenn Sie die REST-API direkt verwenden, müssen Sie diese Werte in eine Datei kombinieren. Das Format dieses JSON-Dokuments sieht folgendermaßen aus:

    {
        "properties": {
            "template": {
                contents of template file
            },
            "mode": "deploymentmode",
            "Parameters": {
                contents of the parameters element from parameters file
            }
        }
    }

Folgendes ist beispielsweise eine Kombination aus den Vorlagen- und Parameterdateien von [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password). Damit wird ein Linux-basierter Cluster erstellt, bei dem das SSH-Benutzerkonto durch ein Kennwort geschützt ist.

    {
        "properties": {
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {
                    "location": {
                        "type": "string",
                        "allowedValues": ["Central US",
                        "East Asia",
                        "East US",
                        "Japan East",
                        "Japan West",
                        "North Europe",
                        "South Central US",
                        "Southeast Asia",
                        "West Europe",
                        "West US"],
                        "metadata": {
                            "description": "The location where all azure resources will be deployed."
                        }
                    },
                    "clusterType": {
                        "type": "string",
                        "allowedValues": ["hadoop",
                        "hbase",
                        "storm",
                        "spark"],
                        "metadata": {
                            "description": "The type of the HDInsight cluster to create."
                        }
                    },
                    "clusterName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the HDInsight cluster to create."
                        }
                    },
                    "clusterLoginUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                        }
                    },
                    "clusterLoginPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "sshUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to remotely access the cluster."
                        }
                    },
                    "sshPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "clusterStorageAccountName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the storage account to be created and be used as the cluster's storage."
                        }
                    },
                    "clusterWorkerNodeCount": {
                        "type": "int",
                        "defaultValue": 4,
                        "metadata": {
                            "description": "The number of nodes in the HDInsight cluster."
                        }
                    }
                },
                "variables": {
                    "defaultApiVersion": "2015-05-01-preview",
                    "clusterApiVersion": "2015-03-01-preview"
                },
                "resources": [{
                    "name": "[parameters('clusterStorageAccountName')]",
                    "type": "Microsoft.Storage/storageAccounts",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('defaultApiVersion')]",
                    "dependsOn": [],
                    "tags": {
                        
                    },
                    "properties": {
                        "accountType": "Standard_LRS"
                    }
                },
                {
                    "name": "[parameters('clusterName')]",
                    "type": "Microsoft.HDInsight/clusters",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('clusterApiVersion')]",
                    "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                    "tags": {
                        
                    },
                    "properties": {
                        "clusterVersion": "3.2",
                        "osType": "Linux",
                        "clusterDefinition": {
                            "kind": "[parameters('clusterType')]",
                            "configurations": {
                                "gateway": {
                                    "restAuthCredential.isEnabled": true,
                                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                                }
                            }
                        },
                        "storageProfile": {
                            "storageaccounts": [{
                                "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                                "isDefault": true,
                                "container": "[parameters('clusterName')]",
                                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                            }]
                        },
                        "computeProfile": {
                            "roles": [{
                                "name": "headnode",
                                "targetInstanceCount": "2",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            },
                            {
                                "name": "workernode",
                                "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            }]
                        }
                    }
                }],
                "outputs": {
                    "cluster": {
                        "type": "object",
                        "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                    }
                }
            },
            "mode": "incremental",
            "Parameters": {
                "location": {
                    "value": "North Europe"
                },
                "clusterName": {
                    "value": "newclustername"
                },
                "clusterType": {
                    "value": "hadoop"
                },
                "clusterStorageAccountName": {
                    "value": "newstoragename"
                },
                "clusterLoginUserName": {
                    "value": "admin"
                },
                "clusterLoginPassword": {
                    "value": "changeme"
                },
                "sshUserName": {
                    "value": "sshuser"
                },
                "sshPassword": {
                    "value": "changeme"
                }
            }
        }
    }

Dieses Beispiel wird in den Schritten im vorliegenden Dokument verwendet. Sie müssen den Platzhalter _values_ im Abschnitt __Parameters__ am Ende des Dokuments durch die Werte ersetzen, die Sie für Ihren Cluster verwenden möchten.

##Anmelden bei Ihrem Azure-Abonnement

Führen Sie die Schritte aus, die unter [Herstellen einer Verbindung mit einem Azure-Abonnement von der Azure-Befehlszeilenschnittstelle (Azure-CLI)](../xplat-cli-connect.md) dokumentiert sind, und stellen Sie über die `azure login`-Methode eine Verbindung mit Ihrem Abonnement her.

##Erstellen eines Dienstprinzipals

> [AZURE.NOTE] Diese Schritte stellen eine verkürzte Version der Informationen im Abschnitt _Authentifizieren des Dienstprinzipals mit einem Kennwort – Azure-Befehlszeilenschnittstelle_ des Dokuments [Authentifizieren eines Dienstprinzipals mit dem Azure Resource Manager](../resource-group-authenticate-service-principal.md#authenticate-service-principal-with-password---azure-cli). Mit diesen Schritten erstellen Sie einen neuen Dienstprinzipal, der zum Authentifizieren der REST-API-Anforderungen für das Erstellen von Azure-Ressourcen wie einem HDInsight-Cluster verwendet werden kann.

1. Verwenden Sie an einer Befehlszeile oder in einem Terminal oder einer Shell den folgenden Befehl, um Ihre Azure-Abonnements aufzulisten.

        azure account list
        
    Wählen Sie in der Liste das Abonnement aus, das Sie verwenden möchten, und notieren Sie sich den Wert in der Spalte __ID__. Dies ist die __Abonnement-ID__, die in den meisten Schritten in diesem Dokument verwendet wird.

2. Erstellen Sie eine neue Anwendung in Azure Active Directory.

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
        
    Ersetzen Sie die Werte für `--name`, `--home-page` und `--identifier-uris` durch Ihre eigenen Werte. Geben Sie ein Kennwort für den neuen Active Directory-Eintrag an.
    
    > [AZURE.NOTE] Da Sie diese Anwendung für die Authentifizierung über einen Dienstprinzipal erstellen, müssen die Werte `--home-page` und `--identifier-uris` nicht auf eine tatsächliche Webseite im Internet verweisen. Es muss sich lediglich um eindeutige URIs handeln.
    
    Speichern Sie den Wert von __AppId__ aus den zurückgegebenen Daten.
    
        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK
    
3. Erstellen Sie einen Dienstprinzipal mit der zuvor zurückgegebenen __AppId__.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
     Speichern Sie den Wert von __Object Id__ aus den zurückgegebenen Daten.
     
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
4. Weisen Sie dem Dienstprinzipal mit dem zuvor zurückgegebenen __Object ID__-Wert die Rolle __Besitzer__ zu. Sie müssen außerdem die __Abonnement-ID__ verwenden, die Sie zuvor erhalten haben.
    
        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Owner -c /subscriptions/{SubscriptionID}/
        
    Nach Abschluss dieses Befehls hat der Dienstprinzipal Besitzerzugriff auf die angegebene Abonnement-ID.

##Abrufen eines Authentifizierungstokens

1. Versuchen Sie, mit dem folgenden Verfahren die __Mandanten-ID__ für Ihr Abonnement zu ermitteln.

        azure account show -s <subscription ID>
        
    Suchen Sie in den zurückgegebenen Daten die __Mandanten-ID__.
    
        info:    Executing command account show
        data:    Name                        : MyAzureAccount
        data:    ID                          : 45a1014d-0f27-25d2-b838-b8f373d6d52e
        data:    State                       : Enabled
        data:    Tenant ID                   : 22f988bf-56f1-41af-91ab-3d7cd011db47
        data:    Is Default                  : true
        data:    Environment                 : AzureCloud
        data:    Has Certificate             : No
        data:    Has Access Token            : Yes
        data:    User name                   : myname@contoso.org
        data:    
        info:    account show command OK

2. Generieren Sie ein neues Token mit der Azure-REST-API.

        curl -X "POST" "https://login.microsoftonline.com/TenantID/oauth2/token" \
        -H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
        -H "Content-Type: application/x-www-form-urlencoded" \
        --data-urlencode "client_id=AppID" \
        --data-urlencode "grant_type=client_credentials" \
        --data-urlencode "client_secret=password" \
        --data-urlencode "resource=https://management.azure.com/"
    
    Ersetzen Sie __TenantID__, __AppID__ und __Kennwort__ mit den Werten, die abgerufen oder zuvor verwendet wurden.

    Wenn die Anforderung erfolgreich ist, erhalten Sie eine 2xx-Antwort, deren Text ein JSON-Dokument enthält.

    Das von dieser Anforderung zurückgegebene JSON-Dokument enthält ein Element namens __access\_token__. Der Wert dieses Elements ist das Zugriffstoken, das zur Authentifizierung der in den nächsten Abschnitten dieses Dokuments verwendeten Anforderungen erforderlich ist.
    
        {
            "token_type":"Bearer",
            "expires_in":"3599",
            "expires_on":"1463409994",
            "not_before":"1463406094",
            "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
        }

##Erstellen einer Ressourcengruppe

Gehen Sie wie folgt vor, um eine neue Ressourcengruppe zu erstellen. Sie müssen zuerst die Gruppe erstellen, bevor Sie die einzelnen Ressourcen, wie z. B. den HDInsight-Cluster, erstellen können.

* Ersetzen Sie __SubscriptionID__ durch die Abonnement-ID, die Sie während der Erstellung des Dienstprinzipals erhalten haben.
* Ersetzen Sie __AccessToken__ durch das Zugriffstoken, das Sie im vorherigen Schritt erhalten haben.
* Ersetzen Sie __DataCenterLocation__ durch das Rechenzentrum, in dem Sie die Ressourcengruppe und die Ressourcen erstellen möchten. Beispiel: "USA, Mitte/Süden".
* Ersetzen Sie __ResourceGroupName__ durch den Namen, den Sie für diese Gruppe verwenden möchten.

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName?api-version=2015-01-01" \
    -H "Authorization: Bearer AccessToken" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DataCenterLocation"
}'
```

Wenn die Anforderung erfolgreich ist, erhalten Sie eine 2xx-Antwort, deren Text ein JSON-Dokument mit Informationen zur Gruppe enthält. Das `"provisioningState"`-Element enthält den Wert `"Succeeded"`.

##Erstellen einer Bereitstellung

Gehen Sie wie folgt vor, um die Clusterkonfiguration (Vorlage und Parameterwerte) in der Ressourcengruppe bereitzustellen.

* Ersetzen Sie __SubscriptionID__ und __AccessToken__ durch die oben verwendeten Werte.
* Ersetzen Sie __ResourceGroupName__ durch den Namen der Ressourcengruppe, die Sie im vorherigen Abschnitt erstellt haben.
* Ersetzen Sie __DeploymentName__ durch den Namen, den Sie für diese Bereitstellung verwenden möchten.

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [AZURE.NOTE] Wenn Sie das JSON-Dokument mit der Vorlage und den Parametern in eine Datei gespeichert haben, können Sie anstelle von `-d "{ template and parameters}"` Folgendes verwenden:
>
> `--data-binary "@/path/to/file.json"`

Wenn die Anforderung erfolgreich ist, erhalten Sie eine 2xx-Antwort, deren Text ein JSON-Dokument mit Informationen zum Bereitstellungsvorgang enthält.

> [AZURE.IMPORTANT] Beachten Sie, dass die Bereitstellung zu diesem Zeitpunkt übermittelt, aber noch nicht abgeschlossen wurde. Es kann mehrere Minuten dauern (in der Regel etwa 15), bis die Bereitstellung abgeschlossen ist.

##Überprüfen des Bereitstellungsstatus

Gehen Sie wie folgt vor, um den Status der Bereitstellung zu prüfen.

* Ersetzen Sie __SubscriptionID__ und __AccessToken__ durch die oben verwendeten Werte.
* Ersetzen Sie __ResourceGroupName__ durch den Namen der Ressourcengruppe, die Sie im vorherigen Abschnitt erstellt haben.

```
curl -X "GET" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json"
```

Damit wird ein JSON-Dokument mit Informationen zum Bereitstellungsvorgang zurückgegeben. Das `"provisioningState"`-Element enthält den Status der Bereitstellung; wenn dieses Element den Wert `"Succeeded"` enthält, wurde die Bereitstellung erfolgreich abgeschlossen. Jetzt kann Ihr Cluster verwendet werden.

##Nächste Schritte

Nachdem Sie einen HDInsight-Cluster erfolgreich erstellt haben, nutzen Sie die folgenden Informationen, um zu erfahren, wie Sie mit Ihrem Cluster arbeiten.

###Hadoop-Cluster

* [Verwenden von Hive mit HDInsight](hdinsight-use-hive.md)
* [Verwenden von Pig mit HDInsight](hdinsight-use-pig.md)
* [Verwenden von MapReduce mit HDInsight](hdinsight-use-mapreduce.md)

###HBase-Cluster

* [Erste Schritte mit HBase in HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Entwickeln von Java-Anwendungen für HBase in HDInsight](hdinsight-hbase-build-java-maven-linux.md)

###Storm-Cluster

* [Entwickeln von Java-Topologien für Storm in HDInsight](hdinsight-storm-develop-java-topology.md)
* [Verwenden von Python-Komponenten in Storm in HDInsight](hdinsight-storm-develop-python-topology.md)
* [Bereitstellen und Überwachen von Topologien mit Storm in HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---HONumber=AcomDC_0727_2016-->