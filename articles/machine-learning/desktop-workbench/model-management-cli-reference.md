---
title: Referenz zur Befehlszeilenschnittstelle für die Azure Machine Learning-Modellverwaltung | Microsoft-Dokumentation
description: Referenz zur Befehlszeilenschnittstelle für die Azure Machine Learning-Modellverwaltung
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 11/08/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 326f71ef18934d95771e6de99faeedc437139d9e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46949246"
---
# <a name="model-management-command-line-interface-reference"></a>Referenz zur Befehlszeilenschnittstelle für die Modellverwaltung

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


## <a name="base-cli-concepts"></a>Grundlegende CLI-Konzepte:

    account : Manage model management accounts. 
    env     : Manage compute environments.
    image   : Manage operationalization images.
    manifest: Manage operationalization manifests.
    model   : Manage operationalization models.
    service : Manage operationalized services.

## <a name="account-commands"></a>Kontobefehle
Ein Modellverwaltungskonto ist für die Nutzung der Dienste erforderlich, mit denen Sie Modelle bereitstellen und verwalten können. Geben Sie `az ml account modelmanagement -h` an, um die folgende Liste anzuzeigen:

    create: Create a Model Management Account.
    delete: Delete a specified Model Management Account.
    list  : Gets the Model Management Accounts in the current subscriptiong.
    set   : Set the active Model Management Account.
    show  : Show a Model Management Account.
    update: Update an existing Model Management Account.

**Erstellen des Kontos für die Modellverwaltung**

Erstellen Sie das Konto für die Modellverwaltung zur Abrechnung mithilfe des folgenden Befehls:

`az ml account modelmanagement create --location [Azure region e.g. eastus2] --name [new account name] --resource-group [resource group name to store the account in]`

Lokale Argumente:

    --location -l       [Required]: Resource location.
    --name -n           [Required]: Name of the model management account.
    --resource-group -g [Required]: Resource group to create the model management account in.
    --description -d              : Description of the model management account.
    --sku-instances               : Number of instances of the selected SKU. Must be between 1 and
                                    16 inclusive.  Default: 1.
    --sku-name                    : SKU name. Valid names are S1|S2|S3|DevTest.  Default: S1.
    --tags -t                     : Tags for the model management account.  Default: {}.
    -v                            : Verbosity flag.

## <a name="environment-commands"></a>Umgebungsbefehle

    cluster        : Switch the current execution context to 'cluster'.
    delete         : Delete an MLCRP-provisioned resource.
    get-credentials: List the keys for an environment.
    list           : List all environments in the current subscription.
    local          : Switch the current execution context to 'local'.
    set            : Set the active MLC environment.
    setup          : Sets up an MLC environment.
    show           : Show an MLC resource; if resource_group or cluster_name are not provided, shows
                     the active MLC env.

**Einrichten der Bereitstellungsumgebung**

Für den folgenden Befehl zum Einrichten ist der Zugriffstyp „Mitwirkender“ für das Abonnement erforderlich. Wenn Sie nicht über die erforderlichen Berechtigungen verfügen, benötigen Sie zumindest Zugriff als Mitwirkender auf die Ressourcengruppe, in der die Bereitstellung erfolgt. Hierzu müssen Sie den Ressourcengruppennamen mithilfe des Flags `-g` als Teil des Einrichtungsbefehls angeben. 

Es gibt zwei Optionen für die Bereitstellung: *local* und *cluster*. Das Festlegen des Flags `--cluster` (oder `-c`) ermöglicht die Clusterbereitstellung, bei der ein ACS-Cluster eingerichtet wird. Die grundlegende Syntax für die Einrichtung ist wie folgt:

`az ml env setup [-c] --location [location of environment resources] --name[name of environment]`

Dieser Befehl initialisiert Ihre Azure Machine Learning-Umgebung mit einem Speicherkonto, der ACR-Registrierung und dem Application Insights-Dienst, der in Ihrem Abonnement erstellt wird. Standardmäßig wird die Umgebung nur für lokale Bereitstellungen (ohne ACS) initialisiert, wenn kein Flag angegeben wird. Wenn Sie den Dienst skalieren müssen, geben Sie das Flag `--cluster` (oder `-c`) an, um einen ACS-Cluster zu erstellen.

Befehlsdetails:

    --location -l        [Required]: Location for environment resources; an Azure region, e.g. eastus2.
    --name -n            [Required]: Name of environment to provision.
    --acr -r                       : ARM ID of ACR to associate with this environment.
    --agent-count -z               : Number of agents to provision in the ACS cluster. Default: 3.
    --cert-cname                   : CNAME of certificate.
    --cert-pem                     : Path to .pem file with certificate bytes.
    --cluster -c                   : Flag to provision ACS cluster. Off by default; specify this to force an ACS cluster deployment.
    --key-pem                      : Path to .pem file with certificate key.
    --master-count -m              : Number of master nodes to provision in the ACS cluster. Acceptable values: 1, 3, 5. Default: 1.
    --resource-group -g            : Resource group in which to create compute resource. Is created if it does not exist.
                                     If not provided, resource group is created with 'rg' appended to 'name.'.
    --service-principal-app-id -a  : App ID of service principal to use for configuring ML compute.
    --service-principal-password -p: Password associated with service principal.
    --storage -s                   : ARM ID of storage account to associate with this environment.
    --yes -y                       : Flag to answer 'yes' to any prompts. Command fails if user is not logged in.

Globale Argumente
```
    --debug                        : Increase logging verbosity to show all debug logs.
    --help -h                      : Show this help message and exit.
    --output -o                    : Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query                        : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose                      : Increase logging verbosity. Use --debug for full debug logs.
```
## <a name="model-commands"></a>Modellbefehle

    list
    register
    show

**Registrieren eines Modells**

Befehl zum Registrieren des Modells.

`az ml model register --model [path to model file] --name [model name]`

Befehlsdetails:

    --model -m [Required]: Model to register.
    --name -n  [Required]: Name of model to register.
    --description -d     : Description of the model.
    --tag -t             : Tags for the model. Multiple tags can be specified with additional -t arguments.
    -v                   : Verbosity flag.

Globale Argumente

    --debug              : Increase logging verbosity to show all debug logs.
    --help -h            : Show this help message and exit.
    --output -o          : Output format.  Allowed values: json, jsonc, table, tsv.  Default: json.
    --query              : JMESPath query string. See http://jmespath.org/ for more information and
                           examples.
    --verbose            : Increase logging verbosity. Use --debug for full debug logs.

## <a name="manifest-commands"></a>Manifestbefehle

    create: Create an Operationalization Manifest. This command has two different
            sets of required arguments, depending on if you want to use previously registered
            model/s.
    list
    show

**Erstellen eines Manifests**

Der folgende Befehl erstellt eine Manifestdatei für das Modell. 

`az ml manifest create --manifest-name [your new manifest name] -f [path to code file] -r [runtime for the image, e.g. spark-py]`

Befehlsdetails:

    --manifest-name -n [Required]: Name of the manifest to create.
    -f                 [Required]: The code file to be deployed.
    -r                 [Required]: Runtime of the web service. Valid runtimes are spark-py|python.
    --conda-file -c              : Path to Conda Environment file.
    --dependency -d              : Files and directories required by the service. Multiple
                                   dependencies can be specified with additional -d arguments.
    --manifest-description       : Description of the manifest.
    --schema-file -s             : Schema file to add to the manifest.
    -p                           : A pip requirements.txt file needed by the code file.
    -v                           : Verbosity flag.

Registrierte Modellargumente

    --model-id -i                : [Required] Id of previously registered model to add to manifest.
                                   Multiple models can be specified with additional -i arguments.

Nicht registrierte Modellargumente

    --model-file -m              : [Required] Model file to register. If used, must be combined with
                                   model name.

Globale Argumente

    --debug                      : Increase logging verbosity to show all debug logs.
    --help -h                    : Show this help message and exit.
    --output -o                  : Output format.  Allowed values: json, jsonc, table, tsv.
                                   Default: json.
    --query                      : JMESPath query string. See http://jmespath.org/ for more
                                   information and examples.
    --verbose                    : Increase logging verbosity. Use --debug for full debug logs.


## <a name="image-commands"></a>Imagebefehle

    create: Creates a docker image with the model and its dependencies. This command has two different sets of
            required arguments, depending on if you want to use a previously created manifest.
    list
    show
    usage

**Erstellen eines Images**

Sie können ein Image erstellen, nachdem Sie sein Manifest zuvor erstellt zu haben. 

`az ml image create -n [image name] --manifest-id [the manifest ID]`

Oder Sie können das Manifest und das Image mit einem einzigen Befehl erstellen. 

`az ml image create -n [image name] --model-file [model file or folder path] -f [code file, e.g. the score.py file] -r [the runtime eg.g. spark-py which is the Docker container image base]`

Befehlsdetails:

    --image-name -n [Required]: The name of the image being created.
    --image-description       : Description of the image.
    --image-type              : The image type to create. Defaults to "Docker".
    -v                        : Verbosity flag.

Registrierte Manifestargumente

    --manifest-id             : [Required] Id of previously registered manifest to use in image creation.

Nicht registrierte Manifestargumente

    --conda-file -c           : Path to Conda Environment file.
    --dependency -d           : Files and directories required by the service. Multiple dependencies can
                                be specified with additional -d arguments.
    --model-file -m           : [Required] Model file to register.
    --schema-file -s          : Schema file to add to the manifest.
    -f                        : [Required] The code file to be deployed.
    -p                        : A pip requirements.txt file needed by the code file.
    -r                        : [Required] Runtime of the web service. Valid runtimes are python|spark-py.


## <a name="service-commands"></a>Dienstbefehle
Die folgenden Befehle werden für den Dienst unterstützt. Um die Parameter für jeden Befehl anzuzeigen, verwenden Sie die Option „-h“. Verwenden Sie beispielsweise `az ml service create realtime -h`, um Details zum Erstellungsbefehl anzuzeigen.

    create
    delete
    keys
    list
    logs
    run
    show
    update
    usage

**Erstellen eines Diensts**

Um einen Dienst mit einem zuvor erstellten Image zu erstellen, verwenden Sie den folgenden Befehl:

`az ml service create realtime --image-id [image to deploy] -n [service name]`

Um einen Dienst, ein Manifest und ein Image mit einem einzigen Befehl zu erstellen, verwenden Sie den folgenden Befehl:

`az ml service create realtime --model-file [path to model file(s)] -f [path to model scoring file, e.g. score.py] -n [service name] -r [run time included in the image, e.g. spark-py]`

Befehlsdetails:

    -n                                : [Required] Webservice name.
    --autoscale-enabled               : Enable automatic scaling of service replicas based on request demand.
                                        Allowed values: true, false. False if omitted.  Default: false.
    --autoscale-max-replicas          : If autoscale is enabled - sets the maximum number of replicas.
    --autoscale-min-replicas          : If autoscale is enabled - sets the minimum number of replicas.
    --autoscale-refresh-period-seconds: If autoscale is enabled - the interval of evaluating scaling demand.
    --autoscale-target-utilization    : If autoscale is enabled - target utilization of replicas time.
    --collect-model-data              : Enable model data collection. Allowed values: true, false. False if omitted.  Default: false.
    --cpu                             : Reserved number of CPU cores per service replica (can be fraction).
    --enable-app-insights -l          : Enable app insights. Allowed values: true, false. False if omitted.  Default: false.
    --memory                          : Reserved amount of memory per service replica, in M or G. (ex. 1G, 300M).
    --replica-max-concurrent-requests : Maximum number of concurrent requests that can be routed to a service replica.
    -v                                : Verbosity flag.
    -z                                : Number of replicas for a Kubernetes service.  Default: 1.

Registrierte Image-Argumente

    --image-id                        : [Required] Image to deploy to the service.

Nicht registrierte Image-Argumente

    --conda-file -c                   : Path to Conda Environment file.
    --image-type                      : The image type to create. Defaults to "Docker".
    --model-file -m                   : [Required] The model to be deployed.
    -d                                : Files and directories required by the service. Multiple dependencies can be specified
                                        with additional -d arguments.
    -f                                : [Required] The code file to be deployed.
    -p                                : A pip requirements.txt file of package needed by the code file.
    -r                                : [Required] Runtime of the web service. Valid runtimes are python|spark-py.
    -s                                : Input and output schema of the web service.

Globale Argumente

    --debug                           : Increase logging verbosity to show all debug logs.
    --help -h                         : Show this help message and exit.
    --output -o                       : Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query                           : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose                         : Increase logging verbosity. Use --debug for full debug logs.


Hinweis zum Flag `-d` für das Anfügen von Abhängigkeiten: Wenn Sie den Namen eines Verzeichnisses übergeben, das nicht bereits komprimiert ist (ZIP, TAR usw.), wird dieses Verzeichnis automatisch komprimiert, übergeben und am anderen Ende dekomprimiert. 

Wenn Sie ein Verzeichnis übergeben, das bereits komprimiert ist, wird das Verzeichnis als Datei behandelt und unverändert übergeben. Es wird automatisch dekomprimiert. Es wird erwartet, dass Sie in Ihrem Code dafür sorgen.

**Abrufen von Dienstdetails**

Abrufen von Dienstdetails einschließlich URL und Nutzung (samt Beispieldaten, wenn ein Schema erstellt wurde).

`az ml service show realtime --name [service name]`

Befehlsdetails:

    --id -i    : The service id to show.
    --name -n  : Webservice name.
    -v         : Verbosity flag.

Globale Argumente

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv.  Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.

**Ausführen des Diensts**

`az ml service run realtime -n [service name] -d [input_data]`

Befehlsdetails:

    --id -i    : The service id to show.
    --name -n  : Webservice name.
    -v         : Verbosity flag.

Globale Argumente

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv.  Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.

Get-Help

    az ml service run realtime

Argumente --id -i    : [Erforderlich] Die Dienst-ID, mit der für die Bewertung ein Abgleich erfolgt.
-d         : Die zum Aufrufen des Webdiensts zu verwendenden Daten.
-v         : Flag für Ausführlichkeit.

Globale Argumente

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.
