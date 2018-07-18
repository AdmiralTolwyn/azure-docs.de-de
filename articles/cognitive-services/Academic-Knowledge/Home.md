---
title: Academic Knowledge-API für Microsoft Academic Graph | Microsoft-Dokumentation
description: Verwenden Sie die Academic Knowledge-API, um Benutzerabfragen zu interpretieren und umfassende Informationen aus Academic Graph in Microsoft Cognitive Services abzurufen.
services: cognitive-services
author: mvorvoreanu
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: mivorvor
ms.openlocfilehash: e241f9a87cd58b62eafd754bd3cb4283aa0a1e92
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374019"
---
# <a name="academic-knowledge-api"></a>Academic Knowledge-API

Willkommen bei der Academic Knowledge-API. Mit diesem Dienst können Sie Benutzerabfragen für akademische Inhalte interpretieren und umfassende Informationen aus Microsoft Academic Graph (MAG) abrufen. Bei der MAG-Wissensdatenbank handelt es sich um einen webbasierten heterogenen Entitätsgraph mit Entitäten zur Modellierung akademischer Aktivitäten: Forschungsbereich, Autor, Einrichtung, Dokument, Ort und Ereignis. 

Die MAG-Daten stammen aus dem Bing-Webindex sowie aus einer internen Wissensdatenbank von Bing. Dank der kontinuierlichen Bing-Indizierung enthält diese API immer aktuelle Informationen aus dem Web, die von Bing ermittelt und indiziert wurden. Auf der Grundlage dieses Datasets ermöglichen Academic Knowledge-APIs einen wissensbasierten, interaktiven Dialog, der die reaktive Suche nahtlos mit proaktiven Vorschlägen, umfangreichen Graphsuchergebnissen für Forschungsarbeiten sowie mit Histogrammverteilungen der Attributwerte für eine Gruppe von Dokumenten und den dazugehörigen Entitäten kombiniert.

Weitere Informationen zu Microsoft Academic Graph finden Sie unter [http://aka.ms/academicgraph](http://aka.ms/academicgraph).

Die Academic Knowledge-API wurde von Cognitive Services Preview in Cognitive Services Labs verschoben. Die neue Homepage für das Projekt finden Sie hier: [https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge](https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge). Ihr vorhandener API-Schlüssel kann noch bis zum 24. Mai 2018 verwendet werden. Danach muss ein neuer API-Schlüssel generiert werden. Die kostenpflichtige Vorschau ist nach Ablauf Ihres vorhandenen Schlüssels nicht mehr verfügbar. Sollte die kostenlose Version der API für Ihre Zwecke nicht ausreichend sein, wenden Sie sich an unser Team. 

## <a name="features"></a>Features
Die Academic Knowledge-API umfasst vier zusammengehörige REST-Endpunkte:  
  1. **interpret**: Interpretiert die Zeichenfolge einer Benutzerabfrage in natürlicher Sprache. Gibt mit Anmerkungen versehene Interpretationen zurück, mit denen sich die Benutzereingabe im Suchfeld vorhersagen und automatisch vervollständigen lässt.  
  2. **evaluate**: Wertet einen Abfrageausdruck aus und gibt Academic Knowledge-Entitäten als Ergebnis zurück.  
  3. **calchistogram**: Berechnet ein Histogramm der Verteilung von Attributwerten für die akademischen Entitäten, die von einem Abfrageausdruck zurückgegeben werden (etwa die Verteilung von Zitaten nach Jahr für einen bestimmten Autor).  
  4. **graph search**: Sucht nach einem angegebenen Graphmuster und gibt die passenden Entitäten als Ergebnis zurück.

Gemeinsam bieten diese API-Methoden umfangreiche semantische Suchfunktionen. Nach Angabe einer Benutzerabfragezeichenfolge liefert die Methode **interpret** eine mit Anmerkungen versehene Version der Abfrage sowie einen strukturierten Abfrageausdruck und vervollständigt optional die Benutzerabfrage auf der Grundlage der Semantik der zugrunde liegenden akademischen Daten. Wenn ein Benutzer also beispielsweise die Zeichenfolge *latent s* eingibt, kann die Methode **interpret** eine Reihe von nach Rangfolge sortierten Interpretationen bereitstellen und dem Benutzer Suchvorschläge für den Forschungsbereich *latent semantic analysis*, für das Dokument *latent structure analysis* oder für andere Entitätsausdrücke anzeigen, die mit *latent s* beginnen. Auf der Grundlage dieser Informationen gelangt der Benutzer schnell zu den gewünschten Suchergebnissen.

Mit der Methode **evaluate** kann ein Satz übereinstimmender Dokumententitäten aus der akademischen Wissensdatenbank abgerufen werden, und die Methode **calchistogram** ermöglicht die Berechnung der Verteilung von Attributwerten für einen Satz von Dokumententitäten, die zur weiteren Filterung der Suchergebnisse genutzt werden können.        

Die Methode **graph search** besitzt zwei Modi: *json* und *lambda*. Im Modus *json* können Graphmusterabgleiche auf der Grundlage der durch ein JSON-Objekt angegebenen Graphmuster durchgeführt werden. Im Modus *lambda* können beim Durchlaufen des Graphs serverseitige Berechnungen auf der Grundlage der vom Benutzer angegebenen Lambdaausdrücke durchgeführt werden.

## <a name="getting-started"></a>Erste Schritte 
Eine ausführliche Dokumentation finden Sie in den Unterthemen auf der linken Seite.  Zur besseren Lesbarkeit der Beispiele enthalten die REST-API-Aufrufe Zeichen (etwa Leerzeichen), die nicht im URL-Format codiert wurden.  In Ihrem Code muss allerdings die korrekte URL-Codierung verwendet werden.
