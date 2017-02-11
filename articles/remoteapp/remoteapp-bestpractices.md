---
title: "Bewährte Methoden für Azure RemoteApp | Microsoft Docs"
description: "Bewährte Methoden zum Konfigurieren und Verwenden von Azure RemoteApp"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: b851865b-bec4-4f29-82c0-7b9770c1a520
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 9af9f95e9638eda71fbc4508bea8e35ce5191734
ms.openlocfilehash: 3caabe366db4def4f8332c64d08f86f271c31bb2


---
# <a name="best-practices-for-configuring-and-using-azure-remoteapp"></a>Bewährte Methoden zum Konfigurieren und Verwenden von Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp wird eingestellt. Details finden Sie in der [Ankündigung](https://blogs.technet.microsoft.com/enterprisemobility/2016/08/12/application-remoting-and-the-cloud/) .
> 
> 

Die folgenden Informationen sollen Ihnen bei der Konfiguration und der produktiven Verwendung von Azure RemoteApp behilflich sein.

## <a name="connectivity"></a>Konnektivität
* Verwenden Sie immer die neueste Clientversion. Bei Verwendung älterer Clients können Verbindungsprobleme und andere Beeinträchtigungen auftreten. Durch das Aktivieren automatischer Anwendungsupdates für Ihr Gerät ist sichergestellt, dass immer der neueste Client installiert ist.
* Verwenden Sie immer die stabilste und zuverlässigste Internetverbindung.  
* Verwenden Sie nur unterstützte Proxy-Verbindungen für eine optimale Verbindungsqualität.  Der SOCKS-Proxy wird nicht unterstützt.

## <a name="applications"></a>Anwendungen
* Speichern und schließen Sie RemoteApp-Anwendungen, wenn Sie die Anwendung nicht mehr benötigen. Eine nicht geschlossene Anwendung kann zu Datenverlusten führen.
* Überprüfen Sie benutzerdefinierte Anwendungen, bevor Sie diese in Azure RemoteApp verwenden. Stellen Sie dabei auch sicher, dass sie auf einer Multisession-Plattform funktionieren, keine unnötigen Ressourcen (Speicher und CPU) belegen und dadurch möglicherweise einen anderen Benutzer derselben Sammlung blockieren. Informationen hierzu finden Sie im Whitepaper [Application Compatibility Best Practices for Remote Desktop Services](http://www.dabcc.com/resources/Application%20Compatibility%20Best%20Practices%20for%20Remote%20Desktop%20Services.pdf), das Sie herunterladen können.

## <a name="configuration-and-management"></a>Konfiguration und Verwaltung
* Halten Sie die Vorlagenimages auf dem neuesten Stand, und installieren Sie nach Bedarf Softwareupdates und andere wichtige Hotfixes und Patches. Dadurch ist gewährleistet, dass bei der automatischen Anpassung von Azure RemoteApp an Ihre Kapazität jede Instanz gepatcht wird.  
* Sorgen Sie für eine sichere und zuverlässige Bereitstellung von Active Directory Federation Services (AD FS). Sonst treten bei Clientauthentifizierungen möglicherweise Fehler auf, und die Benutzer können nicht auf Azure RemoteApp zugreifen.
* Achten Sie beim Konfigurieren von Vorlagenimages mit installierten Anwendungen, Rollen oder Features darauf, dass sie keinen Status aufweisen. Sie sollten sich nicht auf eine Instanz der virtuellen Computer in einem RemoteApp-Dienst, der sich in einem permanenten Status befindet, stützen.
  * Speichern Sie alle Benutzerdaten in Benutzerprofilen oder an anderen externen Speicherorten (außerhalb des Diensts), wie z. B. in lokalen Dateifreigaben oder auf OneDrive.
  * Speichern Sie freigegebene Daten an externen Speicherorten (außerhalb des Diensts), wie z. B. in lokalen Dateifreigaben oder auf OneDrive.
  * Konfigurieren Sie systemweite Einstellungen im Vorlagenimage und nicht auf einzelnen virtuellen Computern in einem Dienst.
  * Deaktivieren Sie automatische Softwareupdates für veröffentlichte Anwendungen. Wenden Sie Updates stattdessen manuell auf das Vorlagenimage an, und testen Sie sie, bevor Sie sie über die Vorlage bereitstellen.




<!--HONumber=Feb17_HO2-->


