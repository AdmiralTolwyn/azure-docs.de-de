---
title: "Einschränkungen und bekannte Probleme beim Import von APIs in Azure API Management | Microsoft-Dokumentation"
description: "Hier erhalten Sie Informationen zu bekannten Problemen und Einschränkungen beim Import in Azure API Management bei Verwendung der Formate Open API, WSDL oder WADL."
services: api-management
documentationcenter: 
author: vladvino
manager: vlvinogr
editor: 
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: apipm
ms.openlocfilehash: 4cb6ad53b59b81f906a85027f4ff988bbb78706a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="api-import-restrictions-and-known-issues"></a>Einschränkungen und bekannte Probleme beim Import von APIs
## <a name="about-this-list"></a>Informationen zu dieser Liste
Auch wenn wir jede Anstrengung unternehmen, Ihnen einen reibungslosen und problemfreien Import Ihrer APIs in Azure API Management zu ermöglichen, sind wir gelegentlich gezwungen, Einschränkungen einzurichten, oder es kommt zu Problemen, die behoben werden müssen, damit Sie einen bestimmten Import erfolgreich abschließen können. Diese Probleme werden nachfolgend unter dem jeweiligen Importformat der API beschrieben.

## <a name="open-api"></a>Open API/Swagger
Wenn Ihnen beim Import von Open API-Dokumenten Fehler gemeldet werden, sollten Sie zunächst sicherstellen, dass Sie das Dokument überprüft haben. Diese Überprüfung können Sie mit dem Designer des neuen Azure-Portals (Design – Front-End – Open API Specification Editor) oder mit einem Tool eines Drittanbieters, zum Beispiel mit <a href="http://www.swagger.io">Swagger Editor</a>, vornehmen.

* **Hostname**: Das Attribut für den Hostnamen ist erforderlich.
* **Basispfad**: Das Attribut für den Basispfad ist erforderlich.
* **Schemas**: Ein Schema-Array ist erforderlich. 

## <a name="wsdl"></a>WSDL
WSDL-Dateien werden zur Generierung von SOAP-Pass-Through-APIs verwendet oder dienen als Back-End einer SOAP-to-REST-API.

* **WSDL:Import**: APIs, die dieses Attribut verwenden, werden derzeit nicht unterstützt. Die Kunden sollten die importierten Elemente zu einem Dokument zusammenfassen.
* **Mehrteilige Nachrichten**: Werden derzeit nicht unterstützt.
* **WCF wsHttpBinding:** Für SOAP-Dienste, die mit Windows Communication Foundation erstellt wurden, sollte basicHttpBinding verwendet werden – wsHttpBinding wird nicht unterstützt.
* **MTOM**: Dienste, die MTOM verwenden, funktionieren <em>möglicherweise</em>. Eine offizielle Unterstützung wird derzeit nicht geboten.
* Rekursiv definierte (also beispielsweise auf ein eigenes Array verweisende) **Rekursionstypen** werden nicht unterstützt.

## <a name="wadl"></a>WADL
Derzeit sind keine Probleme beim Import im Format WADL bekannt.


[api-management-management-console]: ./media/api-management-howto-add-operations/api-management-management-console.png
[api-management-operations]: ./media/api-management-howto-add-operations/api-management-operations.png
[api-management-add-operation]: ./media/api-management-howto-add-operations/api-management-add-operation.png
[api-management-http-method]: ./media/api-management-howto-add-operations/api-management-http-method.png
[api-management-url-template]: ./media/api-management-howto-add-operations/api-management-url-template.png
[api-management-url-template-rewrite]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
[api-management-description]: ./media/api-management-howto-add-operations/api-management-description.png
[api-management-caching-tab]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
[api-management-request-parameters]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
[api-management-request-body]: ./media/api-management-howto-add-operations/api-management-request-body.png
[api-management-response-code]: ./media/api-management-howto-add-operations/api-management-response-code.png
[api-management-response-body-content-type]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
[api-management-response-body]: ./media/api-management-howto-add-operations/api-management-response-body.png


[api-management-contoso-api]: ./media/api-management-howto-add-operations/api-management-contoso-api.png

[api-management-add-new-api]: ./media/api-management-howto-add-operations/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-add-operations/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-add-operations/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-add-operations/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-add-operations/api-management-echo-operations.png

[Add an operation]: #add-operation
[Operation caching]: #operation-caching
[Request parameters]: #request-parameters
[Request body]: #request-body
[Responses]: #responses
[Next steps]: #next-steps

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to cache operation results in Azure API Management]: api-management-howto-cache.md
