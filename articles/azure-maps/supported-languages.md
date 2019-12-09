---
title: Unterstützte Sprachen in Azure Maps | Microsoft-Dokumentation
description: Erfahren Sie mehr über unterstützte Sprachen in Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 64197cc85822997926a8011af8cb3b981fa9064d
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286415"
---
# <a name="localization-support-in-azure-maps"></a>Unterstützte Sprachen in Azure Maps

Azure Maps unterstützt basierend auf Land/Region verschiedene Sprachen und Ansichten. Dieser Artikel enthält die unterstützten Sprachen und Ansichten, die Ihnen bei der Implementierung von Azure Maps helfen.


## <a name="azure-maps-supported-languages"></a>Unterstützte Sprachen in Azure Maps

Azure Maps wurde in verschiedene Sprachen für die unterschiedlichen Dienste übersetzt. In der folgenden Tabelle finden Sie die unterstützten Sprachcodes für jeden Dienst.  
  

| id         | NAME                   |  Karten | Suchen, | Routing | Weather | Verkehrsmeldungen | JS-Kartensteuerelement |
|------------|------------------------|:-----:|:------:|:-------:|:--------:|:-----------------:|:--------------:|
| af-ZA      | Afrikaans              |       |    ✓   |    ✓    |         |                   |                |
| ar-SA      | Arabisch                 |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| bn-BD      | Bangla (Bangladesch)    |       |       |         |     ✓    |                   |                |
| bn-IN      | Bangla (Indien)         |       |       |         |     ✓    |                   |                |
| bs-BA      | Bosnisch                 |       |       |         |     ✓    |                   |                |
| eu-ES      | Baskisch                 |       |    ✓   |         |         |                   |                |
| bg-BG      | Bulgarisch              |   ✓   |    ✓   |    ✓    |     ✓     |                   |        ✓       |
| ca-ES      | Katalanisch                |       |    ✓   |         |    ✓      |                   |                |
| zh-HanS    | Chinesisch (vereinfacht)   |       |  zh-CN |         |     zh-CN   |                   |                |
| zh-HanT    | Chinesisch (Hongkong SAR)  |  |   |    |    zh-HK   |                   |           |
| zh-HanT    | Chinesisch (Taiwan)  | zh-TW |  zh-TW |  zh-TW  |    zh-TW   |                   |      zh-TW     |
| hr-HR      | Kroatisch               |       |    ✓   |         |    ✓      |                   |                |
| cs-CZ      | Tschechisch                  |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| da-DK      | Dänisch                 |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| nl-BE      | Niederländisch (Belgien)        |       |    ✓   |         |      ✓    |                   |                |
| nl-NL      | Niederländisch (Niederlande)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-AU      | Englisch (Australien)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-NZ      | Englisch (Neuseeland)  |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-GB      | Englisch (Großbritannien) |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-US      | Englisch (USA)          |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| et-EE      | Estnisch               |       |    ✓   |         |      ✓    |         ✓         |                |
| fil-PH     | Filipino               |       |       |         |     ✓    |                   |                |
| fi-FI      | Finnisch                |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-FR      | Französisch                 |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-CA      | Französisch (Kanada)      |       |    ✓   |         |     ✓     |                   |                |
| gl-ES      | Galizisch               |       |    ✓   |         |         |                   |                |
| de-DE      | Deutsch                 |   ✓   |    ✓   |    ✓    |   ✓      |         ✓         |        ✓       |
| el-GR      | Griechisch                  |   ✓   |    ✓   |    ✓    |    ✓     |         ✓         |        ✓       |
| gu-IN      | Gujarati                |       |       |         |     ✓    |                   |                |
| he-IL      | Hebräisch                 |       |    ✓   |         |     ✓    |         ✓         |                |
| hi-IN      | Hindi                  |       |        |         |     ✓    |                   |                |
| hu-HU      | Ungarisch              |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| is-IS      | Isländisch              |       |       |         |     ✓    |                   |                |
| id-ID      | Indonesisch             |   ✓   |    ✓    |    ✓    |     ✓    |         ✓         |        ✓       |
| it-IT      | Italienisch                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| ja-JP      | Japanisch               |       |        |         |     ✓    |                   |                |
| kn-IN      | Kannada                |       |       |         |     ✓    |                   |                |
| kk-KZ      | Kasachisch                 |       |    ✓   |         |     ✓    |                   |                |
| ko-KR      | Koreanisch                 |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| es-419     | Spanisch (Lateinamerika) |       |    ✓   |         |         |                   |                |
| lv-LV      | Lettisch                |       |    ✓   |         |     ✓    |         ✓         |                |
| lt-LT      | Litauisch             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| mk-MK      | Mazedonisch             |       |       |         |     ✓    |                   |                |
| ms-MY      | Malaiisch (Lateinisch)          |   ✓   |    ✓   |    ✓    |    ✓   |                   |        ✓       |
| mr-IN      | Marathi                 |       |       |         |     ✓    |                   |                |
| nb-NO      | Norwegisch (Bokmål)       |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| NGT        | Neutral Ground Truth – Offizielle Sprachen für alle Regionen in lokalen Schriften, falls vorhanden |   ✓     |        |         |       |        |      ✓          |
| NGT-Latn   | Neutral Ground Truth – Lateinische Exonyme Lateinische Schrift wird verwendet, wenn verfügbar |   ✓     |        |         |         |                |        ✓         |
| pl-PL      | Polnisch                 |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| pt-BR      | Portugiesisch (Brasilien)    |   ✓   |    ✓   |    ✓    |      ✓   |                   |        ✓       |
| pt-PT      | Portugiesisch (Portugal)  |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| pa-IN      | Pandschabi                 |       |       |         |     ✓    |                   |                |
| ro-RO      | Rumänisch               |       |    ✓    |         |     ✓    |         ✓         |                |
| ru-RU      | Russisch                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| sr-Cyrl-RS | Serbisch (Kyrillisch)     |       |   sr-RS  |         |    sr-RS     |                   |                |
| sr-Latn-RS | Serbisch (Lateinisch)        |       |       |         |     sr-latn    |                   |                |
| sk-SK      | Slowakisch             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| sl-SL      | Slowenisch              |   ✓   |    ✓   |    ✓    |     ✓    |                   |        ✓       |
| es-ES      | Spanisch                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| es-MX      | Spanisch (Mexiko)       |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| sv-SE      | Schwedisch                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| ta-IN      | Tamil (Indien)                 |       |       |         |     ✓    |                   |                |
| te-IN      | Telugu (Indien)                 |       |       |         |     ✓    |                   |                |
| th-TH      | Thailändisch                   |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| tr-TR      | Türkisch                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| uk-UA      | Ukrainisch               |       |    ✓   |         |     ✓    |                   |                |
| ur-PK      | Urdu                 |       |       |         |     ✓    |                   |                |
| uz-Latn-UZ | Usbekisch                 |       |       |         |     ✓    |                   |                |
| vi-VN      | Vietnamesisch             |       |    ✓   |         |      ✓    |                  |                |


## <a name="azure-maps-supported-views"></a>Unterstützte Ansichten in Azure Maps

> [!Note]
> Azure Maps wurde in den folgenden Ländern/Regionen am 1. August 2019 veröffentlicht:
>  * Argentinien
>  * Indien
>  * Marokko
>  * Pakistan
>
> Nach dem 1. August 2019 definiert die Parametereinstellung **View** den zurückgegebenen Karteninhalt für die oben aufgeführten neuen Regionen/Länder. Es wird empfohlen, dass Sie den View-Parameter entsprechend den Anforderungen für die REST-APIs und SDKs, die Ihre Dienste verwenden, eingerichtet haben.
>  
>
>  **REST-APIs:**
>  
>  Stellen Sie sicher, dass Sie den View-Parameter wie erforderlich eingerichtet haben. Der View-Parameter gibt an, welcher Satz von geopolitisch umstrittenen Inhalten über die Azure Maps-Dienste zurückgegeben wird. 
>
>  Betroffene Azure Maps REST-Dienste:
>    
>    * Get Map Tile (Kartenkachel abrufen)
>    * Get Map Image (Kartenbild abrufen) 
>    * Get Search Fuzzy (Fuzzy für die Suche abrufen)
>    * Get Search POI (POI für die Suche abrufen)
>    * Get Search POI Category (POI-Kategorie für die Suche abrufen)
>    * Get Search Nearby (Suche in der Nähe abrufen)
>    * Get Search Address (Suchadresse abrufen)
>    * Get Search Address Structured (Suchadresse strukturiert abrufen)
>    * Get Search Address Reverse (Suchadresse invers abrufen)
>    * Get Search Address Reverse Cross Street (Suchadresse für Querstraße invers abrufen)
>    * Post Search Inside Geometry (Suche innerhalb der Geometrie veröffentlichen)
>    * Post Search Address Batch Preview (Batchvorschau für Suchadresse veröffentlichen)
>    * Post Search Address Reverse Batch Preview (Batchvorschau für inverse Suchadresse veröffentlichen)
>    * Post Search Along Route (Suche entlang einer Route veröffentlichen)
>    * Post Search Fuzzy Batch Preview (Batchvorschau für Suchfuzzy veröffentlichen)
>
>    
>  **SDKs:**
>
>  Stellen Sie sicher, dass Sie den View-Parameter wie erforderlich eingerichtet haben und über die neueste Version des Web SDKs und des Android SDKs verfügen. Betroffene SDKs:
>
>    * Azure Maps Web SDK
>    * Azure Maps Android SDK


Der **View**-Parameter von Azure Maps (auch „Benutzerregionsparameter“ genannt) ist ein zweistelliger ISO-3166-Ländercode, der die richtigen Karten für dieses Land/diese Region anzeigt und angibt, welcher Satz von geopolitisch umstrittenen Inhalten über die Dienste von Azure Maps zurückgegeben wird, einschließlich der auf der Karte angezeigten Grenzen und Bezeichnungen. 

Standardmäßig ist der View-Parameter auf **Unified** eingestellt, auch wenn Sie ihn in der Anforderung nicht definiert haben. Es liegt in Ihrer Verantwortung, den Standort Ihrer Benutzer zu bestimmen und dann den „View“-Parameter für diesen Standort entsprechend festzulegen. Alternativ haben Sie die Möglichkeit, „View=Auto“ festzulegen, wodurch die Kartendaten basierend auf der IP-Adresse der Anforderung zurückgibt.  Der „View“-Parameter in Azure Maps muss in Übereinstimmung mit geltenden Gesetzen verwendet werden, einschließlich derjenigen, die sich auf die Kartierung des Landes beziehen, in der Karten, Bilder und andere Daten und Inhalte von Drittanbietern, auf die Sie über Azure Maps zugreifen dürfen, bereitgestellt werden.


Die folgende Tabelle enthält einige unterstützte Ansichten.

| Sicht         | BESCHREIBUNG                            |  Karten | Suchen, | JS-Kartensteuerelement |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Vereinigte Arabische Emirate (arabische Ansicht)    |   ✓   |        |     ✓          |
| AR           | Argentinien (argentinische Ansicht)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahrain (arabische Ansicht)                 |   ✓   |        |     ✓          |
| IN           | Indien (indische Ansicht)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Irak (arabische Ansicht)                    |   ✓   |        |     ✓          |
| JO           | Jordanien (arabische Ansicht)                  |   ✓   |        |     ✓          |
| KW           | Kuwait (arabische Ansicht)                  |   ✓   |        |     ✓          |
| LB           | Libanon (arabische Ansicht)                 |   ✓   |        |     ✓          |
| MA           | Marokko (marokkanische Ansicht)                |   ✓   |   ✓     |     ✓          |
| OM           | Oman (arabische Ansicht)                    |   ✓   |        |     ✓          |
| PK           | Pakistan (pakistanische Ansicht)              |   ✓   |    ✓    |     ✓          |
| PS           | Palästinensische Autonomiegebiete (arabische Ansicht)    |   ✓   |        |     ✓          |
| QA           | Katar (arabische Ansicht)                   |   ✓   |        |     ✓          |
| SA           | Saudi-Arabien (arabische Ansicht)            |   ✓   |        |     ✓          |
| SY           | Syrien (arabische Ansicht)                   |   ✓   |        |     ✓          |
| YE           | Jemen (arabische Ansicht)                   |   ✓   |        |     ✓          |
| Auto         | Gibt die Kartendaten basierend auf der IP-Adresse der Anforderung zurück.|   ✓   |    ✓   |     ✓          |
| Einheitlich      | Einheitliche Ansicht (andere)                  |   ✓   |   ✓     |     ✓          |
