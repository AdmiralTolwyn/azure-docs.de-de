---
title: "Einrichten eines Geräts mithilfe des Azure IoT Hub Device Provisioning-Diensts | Microsoft-Dokumentation"
description: "Einrichten eines bereitzustellenden Geräts über den IoT Hub Device Provisioning-Dienst während des Geräteherstellungsprozesses"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 835a54f147b9ea543df21e7dfeb226ac42aceda3
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2017
---
# <a name="set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>Einrichten eines bereitzustellenden Geräts mithilfe des Azure IoT Hub Device Provisioning-Diensts

Im vorherigen Tutorial haben Sie erfahren, wie Sie den Azure IoT Hub Device Provisioning-Dienst einrichten, um Ihre Geräte automatisch für IoT Hub bereitzustellen. Dieses Tutorial enthält Anweisungen zum Einrichten Ihres Geräts während des Herstellungsprozesses, sodass Sie den Device Provisioning-Dienst für Ihr Gerät basierend auf dem jeweiligen [Hardwaresicherheitsmodul (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security) konfigurieren können. Das Gerät kann dann eine Verbindung mit Ihrem Device Provisioning-Dienst herstellen, wenn es zum ersten Mal gestartet wird. In diesem Tutorial werden folgende Prozesse erläutert:

> [!div class="checklist"]
> * Auswählen eines Hardwaresicherheitsmoduls
> * Erstellen eines Device Provisioning-Client-SDK für das ausgewählte HSM
> * Extrahieren der Sicherheitsartefakte
> * Einrichten der Device Provisioning Service-Dienstkonfiguration auf dem Gerät

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie fortfahren, erstellen Sie anhand der Anweisungen im Tutorial [Einrichten einer Cloud für die Gerätebereitstellung](./tutorial-set-up-cloud.md) Ihre Device Provisioning-Dienstinstanz und eine IoT Hub-Instanz.


## <a name="select-a-hardware-security-module"></a>Auswählen eines Hardwaresicherheitsmoduls

Das [Client-SDK des Device Provisioning-Diensts](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client) bietet Unterstützung für zwei Arten von Hardwaresicherheitsmodulen (kurz HSMs): 

- [Trusted Platform Module (TPM)](https://en.wikipedia.org/wiki/Trusted_Platform_Module)
    - TPM ist ein etablierter Standard für die meisten Windows-basierten Geräteplattformen sowie für einige Linux/Ubuntu-basierte Geräte. Als Gerätehersteller können Sie dieses HSM auswählen, wenn eines der folgenden Betriebssysteme auf Ihren Geräten ausgeführt wird und wenn Sie nach einem etablierten Standard für HSMs suchen. Bei TPM-Chips können Sie nur jedes Gerät einzeln beim Device Provisioning-Dienst registrieren. Für Entwicklungszwecke können Sie den TPM-Simulator auf dem Windows- oder Linux-Entwicklungscomputer verwenden.

- [X.509](https://cryptography.io/en/latest/x509/)-basierte Hardwaresicherheitsmodule 
    - X.509-basierte HSMs sind relativ neuere Chips. So werden die Arbeiten bei Microsoft gegenwärtig auf RIoT- oder DICE-Chips fortgeführt, bei denen die X.509-Zertifikate implementiert werden. Mit X.509-Chips können Sie Massenregistrierungen im Portal durchführen. Darüber hinaus werden bestimmte Windows-fremde Betriebssysteme wie mbed OS unterstützt. Für Entwicklungszwecke unterstützt das Client-SDK des Device Provisioning-Diensts einen X.509-Gerätesimulator. 

Als Gerätehersteller müssen Sie Hardwaresicherheitsmodule/-chips auswählen, die auf einem der voranstehenden Typen basieren. Andere HSM-Typen werden derzeit nicht im Client-SDK des Device Provisioning-Diensts unterstützt.   


## <a name="build-device-provisioning-client-sdk-for-the-selected-hsm"></a>Erstellen eines Device Provisioning-Client-SDK für das ausgewählte HSM

Das Client-SDK des Device Provisioning-Diensts unterstützt bei der Implementierung des ausgewählten Sicherheitsmechanismus in der Software. In den folgenden Schritten wird gezeigt, wie das SDK für den ausgewählten HSM-Chip verwendet wird:

1. Wenn Sie den [Schnellstart zum Erstellen eines simulierten Geräts](./quick-create-simulated-device.md) befolgt haben, ist das Setup für die Erstellung des SDK bereit. Falls nicht, führen Sie die ersten vier Schritte im Abschnitt [Vorbereiten der Entwicklungsumgebung](./quick-create-simulated-device.md#setupdevbox) durch. Bei diesen Schritten wird das GitHub-Repository für das Client-SDK des Device Provisioning-Diensts geklont sowie das Buildtool `cmake` installiert. 

1. Erstellen Sie mit einem der folgenden Befehle in der Eingabeaufforderung das SDK für den HSM-Typ, den Sie für Ihr Gerät ausgewählt haben:
    - Für TPM-Geräte:
        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON ..
        ```

    - Für TPM-Simulatoren:
        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
        ```

    - Für X.509-Geräte und -Simulatoren:
        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON ..
        ```

1. Das SDK bietet standardmäßig Unterstützung für Geräte, auf denen Windows- oder Ubuntu-Implementierungen für TPM- und X.509-HSMs ausgeführt werden. Fahren Sie für diese unterstützten HSMs mit dem Abschnitt [Extrahieren der Sicherheitsartefakte](#extractsecurity) unten durch. 
 
## <a name="support-custom-tpm-and-x509-devices"></a>Unterstützte benutzerdefinierte TPM- und X.509-Geräte

Das Client-SDK des Device Provisioning-Systems bietet keine standardmäßige Unterstützung für TPM- und X.509-Geräte, auf denen weder Windows noch Ubuntu ausgeführt wird. Für solche Geräte müssen Sie den benutzerdefinierten Code für Ihren jeweiligen HSM-Chip schreiben, wie in den folgenden Schritten gezeigt wird:

### <a name="develop-your-custom-repository"></a>Entwickeln eines benutzerdefinierten Repositorys

1. Entwickeln Sie eine Bibliothek für den Zugriff auf Ihr HSM. Bei diesem Projekt muss eine statische Bibliothek zur Verwendung für das Device Provisioning-Dienst-SDK erzeugt werden.
1. Ihre Bibliothek muss die in der folgenden Headerdatei definierten Funktionen implementieren: a. Implementieren Sie bei einem benutzerdefinierten TPM-Gerät die im [Dokument für benutzerdefinierte HSMs](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-tpm-api) definierten Funktionen.
    b. Implementieren Sie bei einem benutzerdefinierten X.509-Gerät die im [Dokument für benutzerdefinierte HSMs](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-x509-api) definierten Funktionen. 

### <a name="integrate-with-the-device-provisioning-service-client"></a>Integrieren in den Device Provisioning-Dienstclient

Sobald erfolgreich eigenständig Repositorys von Ihrer Bibliothek erstellt werden, können Sie zum IoThub C-SDK wechseln und einen Link zu Ihrer Bibliothek erstellen:

1. Geben Sie das benutzerdefinierte HSM-GitHub-Repository, den Bibliothekspfad und den zugehörigen Namen in den folgenden cmake-Befehl ein:
    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=<path_and_name_of_library> <PATH_TO_AZURE_IOT_SDK>
    ```
   
1. Öffnen Sie das SDK in Visual Studio, und erstellen Sie die Bibliothek. 

    - Die SDK-Bibliothek wird während des Buildprozesses kompiliert.
    - Das SDK versucht eine Verknüpfung mit dem benutzerdefinierten HSM herzustellen, das im cmake-Befehl definiert ist.

1. Führen Sie das Beispiel `\azure-iot-sdk-c\provisioning_client\samples\prov_dev_client_ll_sample\prov_dev_client_ll_sample.c` aus, um zu überprüfen, ob Ihr HSM ordnungsgemäß implementiert ist.

<a id="extractsecurity"></a>
## <a name="extract-the-security-artifacts"></a>Extrahieren der Sicherheitsartefakte

Der nächste Schritt besteht darin, die Sicherheitsartefakte für das HSM auf Ihrem Gerät zu extrahieren.

1. Bei einem TPM-Gerät müssen Sie den zugehörigen **Endorsement Key** über den Hersteller des TPM-Chips in Erfahrung bringen. Sie können eine eindeutige **Registrierungs-ID** für Ihr TPM-Gerät ableiten, indem Sie den Hash des Endorsement Key abrufen. 
2. Bei einem X.509-Gerät müssen Sie die für Ihre Geräte ausgestellten Zertifikate abrufen – hierbei handelt es sich um Endentitätszertifikate für individuelle Geräteregistrierungen, bei Gruppenregistrierungen von Geräten hingegen um Stammzertifikate.

Diese Sicherheitsartefakte sind für die Registrierung Ihrer Geräte beim Device Provisioning-Dienst erforderlich. Der Bereitstellungsdienst wartet dann, bis eines dieser Geräte gestartet und zu einem beliebigen Zeitpunkt eine Verbindung mit diesem hergestellt wird. Informationen zum Erstellen von Registrierungen mithilfe dieser Sicherheitsartefakte finden Sie im Artikel [Verwalten von Geräteregistrierungen](how-to-manage-enrollments.md). 

Wenn Ihr Gerät zum ersten Mal gestartet wird, interagiert das Client-SDK zur Extraktion der Sicherheitsartefakte vom Gerät mit Ihrem Chip und überprüft die Registrierung bei Ihrem Device Provisioning-Dienst. 


## <a name="set-up-the-device-provisioning-service-configuration-on-the-device"></a>Einrichten der Device Provisioning Service-Dienstkonfiguration auf dem Gerät

Der letzte Schritt im Geräteherstellungsprozess besteht darin, eine Anwendung zu schreiben, die das Gerät mithilfe des Client-SDK des Device Provisioning-Dienst beim Dienst registriert. Dieses SDK bietet die folgenden APIs für Ihre zu verwendenden Anwendungen:

```C
// Creates a Provisioning Client for communications with the Device Provisioning Client Service
PROV_DEVICE_LL_HANDLE Prov_Device_LL_Create(const char* uri, const char* scope_id, PROV_DEVICE_TRANSPORT_PROVIDER_FUNCTION protocol)

// Disposes of resources allocated by the provisioning Client.
void Prov_Device_LL_Destroy(PROV_DEVICE_LL_HANDLE handle)

// Asynchronous call initiates the registration of a device.
PROV_DEVICE_RESULT Prov_Device_LL_Register_Device(PROV_DEVICE_LL_HANDLE handle, PROV_DEVICE_CLIENT_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, PROV_DEVICE_CLIENT_REGISTER_STATUS_CALLBACK reg_status_cb, void* status_user_ctext)

// Api to be called by user when work (registering device) can be done
void Prov_Device_LL_DoWork(PROV_DEVICE_LL_HANDLE handle)

// API sets a runtime option identified by parameter optionName to a value pointed to by value
PROV_DEVICE_RESULT Prov_Device_LL_SetOption(PROV_DEVICE_LL_HANDLE handle, const char* optionName, const void* value)
```

Denken Sie daran, die Variablen `uri` und `id_scope` vor deren Verwendung zu initialisieren, wie unter [Simulieren der ersten Startsequenz für den Geräteabschnitt dieses Schnellstarts](./quick-create-simulated-device.md#firstbootsequence) erläutert wird. Die Registrierungs-API des Device Provisioning-Clients `Prov_Device_LL_Create` stellt eine Verbindung mit dem globalen Device Provisioning-Dienst her. Der *ID-Bereich* wird vom Dienst generiert und stellt Eindeutigkeit sicher. Er ist unveränderlich und wird zur eindeutigen Identifizierung der Registrierungs-IDs verwendet. Durch den `iothub_uri` kann die Registrierungs-API des IoT Hub-Clients `IoTHubClient_LL_CreateFromDeviceAuth` eine Verbindung mit der richtigen IoT Hub-Instanz herstellen. 


Mithilfe dieser APIs kann Ihr Gerät eine Verbindung mit dem Device Provisioning-Dienst herstellen und bei diesem registriert werden, wenn es gestartet wird. Zudem kann es Informationen zu IoT Hub abrufen und eine Verbindung mit dieser herstellen. Die Datei `provisioning_client/samples/prov_client_ll_sample/prov_client_ll_sample.c` zeigt, wie diese APIs verwendet werden. Im Allgemeinen müssen Sie das folgende Framework für die Clientregistrierung erstellen:

```C
static const char* global_uri = "global.azure-devices-provisioning.net";
static const char* id_scope = "[ID scope for your provisioning service]";
...
static void register_callback(DPS_RESULT register_result, const char* iothub_uri, const char* device_id, void* context)
{
    USER_DEFINED_INFO* user_info = (USER_DEFINED_INFO *)user_context;
    ...
    user_info. reg_complete = 1;
}
static void registation_status(DPS_REGISTRATION_STATUS reg_status, void* user_context)
{
}
int main()
{
    ...
    SECURE_DEVICE_TYPE hsm_type;
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    prov_dev_security_init(hsm_type); // initialize your HSM 

    prov_transport = Prov_Device_HTTP_Protocol;
    
    PROV_CLIENT_LL_HANDLE handle = Prov_Device_LL_Create(global_uri, id_scope, prov_transport); // Create your provisioning client

    if (Prov_Client_LL_Register_Device(handle, register_callback, &user_info, register_status, &user_info) == IOTHUB_DPS_OK) {
        do {
        // The register_callback is called when registration is complete or fails
            Prov_Client_LL_DoWork(handle);
        } while (user_info.reg_complete == 0);
    }
    Prov_Client_LL_Destroy(handle); // Clean up the Provisioning client
    ...
    iothub_client = IoTHubClient_LL_CreateFromDeviceAuth(user_info.iothub_uri, user_info.device_id, transport); // Create your IoT hub client and connect to your hub
    ...
}
```

Sie können die Registrierungsanwendung Ihres Device Provisioning-Dienstclients zuerst über ein simuliertes Gerät mithilfe von Testdiensteinstellungen anpassen. Sobald Ihre Anwendung ordnungsgemäß in der Testumgebung funktioniert, können Sie sie für das jeweilige Gerät erstellen und die ausführbare Datei in Ihr Geräteimage kopieren. Starten Sie noch nicht das Gerät. Bevor Sie das Gerät starten, müssen Sie [das Gerät mit dem Device Provisioning-Dienst registrieren](./tutorial-provision-device-to-hub.md#enrolldevice). Dieser Vorgang wird in den nachfolgenden Schritten veranschaulicht. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

An dieser Stelle haben Sie eventuell den Device Provisioning- und IoT Hub-Dienst im Portal eingerichtet. Wenn Sie die Einrichtung des Device Provisioning-Diensts abbrechen und/oder einen dieser Dienste zu einem späteren Zeitpunkt verwenden möchten, sollten Sie sie deaktivieren, damit keine unnötigen Kosten entstehen.

1. Klicken Sie im Azure-Portal im Menü auf der linken Seite auf **Alle Ressourcen**, und wählen Sie Ihren Device Provisioning-Dienst aus. Klicken Sie im oberen Bereich des Blatts **Alle Ressourcen** auf **Löschen**.  
1. Klicken Sie im Azure-Portal im Menü auf der linken Seite auf **Alle Ressourcen**, und wählen Sie Ihre IoT Hub-Instanz aus. Klicken Sie im oberen Bereich des Blatts **Alle Ressourcen** auf **Löschen**.  


## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Auswählen eines Hardwaresicherheitsmoduls
> * Erstellen eines Device Provisioning-Client-SDK für das ausgewählte HSM
> * Extrahieren der Sicherheitsartefakte
> * Einrichten der Device Provisioning Service-Dienstkonfiguration auf dem Gerät

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie das Gerät auf IoT Hub bereitgestellt wird, indem Sie es beim Azure IoT Hub Device Provisioning-Dienst für die automatische Bereitstellung registrieren.

> [!div class="nextstepaction"]
> [Bereitstellen des Geräts auf IoT Hub](tutorial-provision-device-to-hub.md)

