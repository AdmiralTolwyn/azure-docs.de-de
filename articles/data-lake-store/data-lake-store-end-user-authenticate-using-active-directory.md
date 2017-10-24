---
title: 'Endbenutzerauthentifizierung: Data Lake Store mit Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie die Authentifizierung von Endbenutzern bei Data Lake Store mithilfe von Azure Active Directory umsetzen.
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/29/2017
ms.author: nitinme
ms.openlocfilehash: 98898675b85d62c97a215f9922f1393001013943
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="end-user-authentication-with-data-lake-store-using-azure-active-directory"></a>Authentifizierung von Endbenutzern bei Data Lake Store mithilfe von Azure Active Directory
> [!div class="op_single_selector"]
> * [Authentifizierung von Endbenutzern](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Dienst-zu-Dienst-Authentifizierung](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Store verwendet Azure Active Directory für die Authentifizierung. Vor dem Erstellen einer Anwendung, die mit Azure Data Lake Store oder Azure Data Lake Analytics funktioniert, müssen Sie entscheiden, wie Sie Ihre Anwendung bei Azure Active Directory (Azure AD) authentifizieren. Sie haben zwei Möglichkeiten:

* Endbenutzerauthentifizierung (dieser Artikel)
* Authentifizierung zwischen Diensten (wählen Sie diese Option aus der obigen Dropdownliste)

Bei beiden Optionen erhält Ihre Anwendung ein OAuth 2.0-Token, das an jede an Azure Data Lake Store oder Azure Data Lake Analytics gestellte Anforderung angefügt wird.

Dieser Artikel erläutert, wie Sie eine **native Azure AD-Anwendung für die Authentifizierung von Endbenutzern** erstellen. Anweisungen zur Konfiguration von Azure AD-Anwendungen für die Dienst-zu-Dienst-Authentifizierung finden Sie unter [Dienst-zu-Dienst-Authentifizierung mit Data Lake Store mithilfe von Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Voraussetzungen
* Ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

* Ihre Abonnement-ID. Sie können sie über das Azure-Portal abrufen. Sie ist beispielsweise auf dem Blatt „Data Lake Store“ des Kontos verfügbar.
  
    ![Abrufen der Abonnement-ID](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* Der Name Ihrer Azure AD-Domäne. Diese können Sie abrufen, indem Sie den Mauszeiger über den rechten oberen Bereich im Azure-Portal bewegen. Im Screenshot unten lautet der Domänenname **contoso.onmicrosoft.com**, und die GUID in Klammern stellt die Mandanten-ID dar. 
  
    ![Abrufen der AAD-Domäne](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

* Ihre Azure-Mandanten-ID. Informationen zum Abrufen der Mandanten-ID finden Sie unter [Abrufen der Mandanten-ID](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).

## <a name="end-user-authentication"></a>Authentifizierung von Endbenutzern
Dieser Authentifizierungsmechanismus wird empfohlen, wenn sich ein Endbenutzer über Azure AD bei Ihrer Anwendung anmelden soll. Die Anwendung kann dann mit der gleichen Zugriffsstufe wie der angemeldete Endbenutzer auf Azure-Ressourcen zugreifen. Ihre Endbenutzer müssen ihre Anmeldeinformationen in regelmäßigen Abständen eingeben, um weiter Zugriff zu haben.

Das Ergebnis der Endbenutzeranmeldung ist, dass Ihre Anwendung über ein Zugriffs- und ein Aktualisierungstoken verfügt. Das Zugriffstoken wird an jede an Data Lake Store oder Data Lake Analytics gestellte Anforderung angefügt und ist standardmäßig eine Stunde gültig. Mithilfe des Aktualisierungstokens kann ein neues Zugriffstoken abgerufen werden, das standardmäßig bis zu zwei Wochen gültig ist. Es gibt zwei Ansätze für die Anmeldung von Endbenutzern.

### <a name="using-the-oauth-20-pop-up"></a>Verwenden des OAuth 2.0-Popupfensters
Ihre Anwendung kann das Einblenden eines OAuth 2.0-Autorisierungsfensters auslösen, in das Endbenutzer ihre Anmeldeinformationen eingeben können. Dieses Popupfenster funktioniert auch mit dem zweistufigen Authentifizierungsprozess von Azure AD. 

> [!NOTE]
> Von der Azure AD Authentication Library (ADAL) für Python oder Java wird diese Methode noch nicht unterstützt.
> 
> 

### <a name="directly-passing-in-user-credentials"></a>Direktes Übergeben von Benutzeranmeldeinformationen
Ihre Anwendung kann Azure AD Benutzeranmeldeinformationen direkt bereitstellen. Diese Methode funktioniert nur mit Benutzerkonten mit Organisations-ID. Sie ist nicht kompatibel mit persönlichen bzw. Live ID-Benutzerkonten, die beispielsweise auf @outlook.com oder @live.com enden. Darüber hinaus ist diese Methode nicht kompatibel mit Benutzerkonten, die die zweistufige Authentifizierung von Azure AD benötigen.

### <a name="what-do-i-need-for-this-approach"></a>Was brauche ich für diesen Ansatz?
* Name Ihrer Azure AD-Domäne Diese Anforderung ist bereits in den in diesem Artikel angegebenen Voraussetzungen aufgeführt.
* Azure AD-Mandanten-ID; Diese Anforderung ist bereits in den in diesem Artikel angegebenen Voraussetzungen aufgeführt.
* **Native Azure AD-Anwendung**
* Anwendungs-ID für die native Azure AD-Anwendung
* Umleitungs-URI für die native Azure AD-Anwendung
* Festlegen der delegierten Berechtigungen


## <a name="step-1-create-an-active-directory-native-application"></a>Schritt 1: Erstellen einer nativen Active Directory-Anwendung

Erstellen und Konfigurieren Sie eine native Azure AD-Anwendung für die Authentifizierung von Endbenutzern mit Azure Data Lake Store mithilfe von Azure Active Directory. Anweisungen finden Sie unter [Erstellen einer Azure AD-Anwendung](../azure-resource-manager/resource-group-create-service-principal-portal.md).

Wenn Sie die Anweisungen unter diesem Link befolgen, stellen Sie sicher, dass Sie als Typ der Anwendung **Nativ** auswählen, wie auf dem folgenden Screenshot gezeigt:

![Erstellen einer Webanwendung](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "Erstellen einer nativen Anwendung")

## <a name="step-2-get-application-id-and-redirect-uri"></a>Schritt 2: Abrufen von Anwendungs-ID und Umleitungs-URI

Erfahren Sie unter [Abrufen der Anwendungs-ID](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key), wie Sie die Anwendungs-ID (im klassischen Azure-Portal auch Client-ID genannt) der nativen Azure AD-Anwendung abrufen.

Führen Sie folgende Schritte aus, um den Umleitungs-URI abzurufen.

1. Wählen Sie im Azure-Portal **Azure Active Directory** aus, klicken Sie auf **App-Registrierungen**, suchen Sie die erstellte native Azure AD-Anwendung, und klicken Sie darauf.

2. Klicken Sie auf dem Blatt **Einstellungen** der Anwendung auf **Umleitungs-URIs**.

    ![Abrufen des Umleitungs-URI](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. Kopieren Sie den angezeigten Wert.


## <a name="step-3-set-permissions"></a>Schritt 3: Legen Sie Berechtigungen fest

1. Wählen Sie im Azure-Portal **Azure Active Directory** aus, klicken Sie auf **App-Registrierungen**, suchen Sie die erstellte native Azure AD-Anwendung, und klicken Sie darauf.

2. Klicken Sie auf dem Blatt **Einstellungen** der Anwendung auf **Erforderliche Berechtigungen**, und klicken Sie auf **Hinzufügen**.

    ![Client-ID](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. Klicken Sie auf dem Blatt **API-Zugriff hinzufügen** auf **Hiermit wählen Sie eine API aus**, klicken Sie auf **Azure Data Lake** und anschließend auf **Auswählen**.

    ![Client-ID](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
 
4.  Klicken Sie auf dem Blatt **API-Zugriff hinzufügen** auf **Berechtigungen auswählen**, aktivieren Sie das Kontrollkästchen, um **Data Lake Store vollen Zugriff zu gewähren**, und klicken Sie auf **Auswählen**.

    ![Client-ID](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    Klicken Sie auf **Fertig**.

5. Wiederholen Sie die letzten beiden Schritte, um ebenfalls Berechtigungen für die **Windows Azure-Service-Verwaltungs-API** zu erteilen.
   
## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie eine native Azure AD-Anwendung erstellt und die erforderlichen Informationen gesammelt, die für Ihre Clientanwendungen nötig sind, die Sie mithilfe von .NET SDK, Java SDK, REST-API usw. erstellen. Sie können nun mit den nachfolgend aufgeführten Artikeln fortfahren, in denen erläutert wird, wie Sie die Azure AD-Webanwendung verwenden, um sich zum ersten Mal mit Data Lake Store authentifizieren und anschließend andere Vorgänge im Store durchführen.

* [Authentifizierung von Endbenutzern bei Data Lake Store mithilfe des Java-SDK](data-lake-store-end-user-authenticate-java-sdk.md)
* [Authentifizierung von Endbenutzern bei Data Lake Store mithilfe von .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
* [Authentifizierung von Endbenutzern bei Data Lake Store mithilfe von Python](data-lake-store-end-user-authenticate-python.md)
* [Authentifizierung von Endbenutzern bei Data Lake Store mithilfe der REST-API](data-lake-store-end-user-authenticate-rest-api.md)

