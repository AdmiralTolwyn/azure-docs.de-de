---
title: Übersicht über die Zugriffssteuerung in Data Lake Store | Microsoft-Dokumentation
description: Grundlegende Informationen zur Funktionsweise der Zugriffssteuerung in Azure Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d16f8c09-c954-40d3-afab-c86ffa8c353d
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: a2e29fd6f2dbd4bd573b780a14bd09c0cd03395f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/28/2018
---
# <a name="access-control-in-azure-data-lake-store"></a>Zugriffssteuerung in Azure Data Lake Store

Das von Azure Data Lake Store implementierte Zugriffssteuerungsmodell leitet sich von HDFS ab, das wiederum vom POSIX-Zugriffssteuerungsmodell abgeleitet wird. In diesem Artikel werden die Grundlagen des Zugriffssteuerungsmodells für Data Lake Store zusammengefasst. Weitere Informationen zum HDFS-Zugriffssteuerungsmodell finden Sie im [Handbuch zu HDFS-Berechtigungen](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

## <a name="access-control-lists-on-files-and-folders"></a>Zugriffssteuerungslisten für Dateien und Ordner

Es gibt zwei Arten von Zugriffssteuerungslisten (Access Control Lists, ACLs): **Zugriffs-ACLs** und **Standard-ACLs**.

* **Zugriffs-ACLs**: Diese Listen steuern den Zugriff auf ein Objekt. Dateien und Ordner verfügen jeweils über Zugriffs-ACLs.

* **Standard-ACLs**: Hierbei handelt es sich um eine Art Vorlage für ACLs, die einem Ordner zugeordnet sind und die Zugriffs-ACLs für alle untergeordneten Elemente bestimmen, die in unter diesem Ordner erstellt werden. Dateien verfügen über keine Standard-ACLs.

![Data Lake Store-ACLs](./media/data-lake-store-access-control/data-lake-store-acls-1.png)

Zugriffs- und Standard-ACLs besitzen die gleiche Struktur.

![Data Lake Store-ACLs](./media/data-lake-store-access-control/data-lake-store-acls-2.png)



> [!NOTE]
> Änderungen an der Standard-ACL für ein übergeordnetes Element haben keine Auswirkungen auf die Zugriffs- oder Standard-ACL bereits vorhandener untergeordneter Elemente.
>
>

## <a name="users-and-identities"></a>Benutzer und Identitäten

Alle Dateien und Ordner verfügen über eigene Berechtigungen für folgende Identitäten:

* Der für die Datei zuständige Benutzer
* Die zuständige Gruppe
* Benannte Benutzer
* Benannte Gruppen
* Alle anderen Benutzer

Die Identitäten von Benutzern und Gruppen sind Azure AD-Identitäten (Azure Active Directory). Sofern nichts anderes angegeben ist, kann ein „Benutzer“ im Data Lake Store-Kontext also entweder ein Azure AD-Benutzer oder eine Azure AD-Sicherheitsgruppe sein.

## <a name="permissions"></a>Berechtigungen

Die Berechtigungen für ein Dateisystemobjekt sind **Lesen**, **Schreiben** und **Ausführen**. Sie können wie in der folgenden Tabelle beschrieben auf Dateien und Ordner angewendet werden:

|            |    File     |   Ordner |
|------------|-------------|----------|
| **Lesen (Read, R)** | Berechtigt zum Lesen von Dateiinhalten | Erfordert **Lesen** und **Ausführen**, um den Inhalt des Ordners aufzulisten.|
| **Schreiben (Write, W)** | Berechtigt zum Schreiben in eine Datei sowie zum Anfügen an eine Datei | Erfordert **Schreiben** und **Ausführen**, um untergeordnete Elemente in einem Ordner zu erstellen. |
| **Ausführen (Execute, X)** | Hat im Kontext von Data Lake Store keine Bedeutung | Erfordert das Durchlaufen der untergeordneten Elemente eines Ordners. |

### <a name="short-forms-for-permissions"></a>Kurzformen für Berechtigungen

**RWX** steht für **Lesen (Read), Schreiben (Write) und Ausführen (Execute)**. Es gibt auch ein noch kürzeres numerisches Format. Hierbei steht **4 für Lesen**, **2 für Schreiben** und **1 für Ausführen**, und Berechtigungen werden als Summe dieser Werte angegeben. Hier einige Beispiele.

| Numerische Form | Kurzform |      Bedeutung     |
|--------------|------------|------------------------|
| 7            | RWX        | Lesen, Schreiben und Ausführen |
| 5            | R-X        | Lesen und Ausführen         |
| 4            | R--        | Lesen                   |
| 0            | ---        | Keine Berechtigungen         |


### <a name="permissions-do-not-inherit"></a>Keine Vererbung bei Berechtigungen

Im von Data Lake Store verwendeten POSIX-basierten Modell werden Berechtigungen für ein Element direkt im Element gespeichert. Berechtigungen für ein Element können also nicht von den übergeordneten Elementen geerbt werden.

## <a name="common-scenarios-related-to-permissions"></a>Allgemeine Szenarien im Zusammenhang mit Berechtigungen

Im Folgenden sind einige allgemeine Szenarien aufgeführt, die veranschaulichen, welche Berechtigungen zum Ausführen bestimmter Vorgänge für ein Data Lake Store-Konto erforderlich sind.

### <a name="permissions-needed-to-read-a-file"></a>Erforderliche Berechtigungen zum Lesen einer Datei

![Data Lake Store-ACLs](./media/data-lake-store-access-control/data-lake-store-acls-3.png)

* Zum Lesen der Datei benötigt der Aufrufer **Leseberechtigungen**.
* Für alle Ordner in der Ordnerstruktur, in denen die Datei enthalten ist, benötigt der Aufrufer **Ausführungsberechtigungen**.

### <a name="permissions-needed-to-append-to-a-file"></a>Erforderliche Berechtigungen zum Anfügen an eine Datei

![Data Lake Store-ACLs](./media/data-lake-store-access-control/data-lake-store-acls-4.png)

* Für die Datei, an die etwas angefügt werden soll, benötigt der Aufrufer **Schreibberechtigungen**.
* Für alle Ordner, in denen die Datei enthalten ist, benötigt der Aufrufer **Ausführungsberechtigungen**.

### <a name="permissions-needed-to-delete-a-file"></a>Erforderliche Berechtigungen zum Löschen einer Datei

![Data Lake Store-ACLs](./media/data-lake-store-access-control/data-lake-store-acls-5.png)

* Für den übergeordneten Ordner benötigt der Aufrufer **Schreib- und Ausführungsberechtigungen**.
* Für alle anderen Ordner im Pfad der Datei benötigt der Aufrufer **Ausführungsberechtigungen**.



> [!NOTE]
> Wenn die beiden obigen Bedingungen erfüllt sind, werden zum Löschen der Datei keine Schreibberechtigungen für die Datei benötigt.
>
>

### <a name="permissions-needed-to-enumerate-a-folder"></a>Erforderliche Berechtigungen zum Auflisten eines Ordners

![Data Lake Store-ACLs](./media/data-lake-store-access-control/data-lake-store-acls-6.png)

* Für den aufzulistenden Ordner benötigt der Aufrufer **Lese- und Ausführungsberechtigungen**.
* Für alle Vorgängerordner benötigt der Aufrufer **Ausführungsberechtigungen**.

## <a name="viewing-permissions-in-the-azure-portal"></a>Anzeigen der Berechtigungen im Azure-Portal

Klicken Sie auf dem Blatt **Daten-Explorer** des Data Lake Store-Kontos auf **Zugriff**, um die ACLs für die jeweilige Datei bzw. den Ordner im Daten-Explorer anzuzeigen. Klicken Sie auf **Zugriff**, um die ACLs für den Ordner **catalog** unter dem Konto **mydatastore** anzuzeigen.

![Data Lake Store-ACLs](./media/data-lake-store-access-control/data-lake-store-show-acls-1.png)

Auf diesem Blatt werden im oberen Bereich die Besitzerberechtigungen angezeigt. (Im Screenshot hat der Besitzer den Namen Bob.) Danach werden die zugewiesenen Zugriffssteuerungslisten für den Zugriff angezeigt. 

![Data Lake Store-ACLs](./media/data-lake-store-access-control/data-lake-store-show-acls-simple-view.png)

Klicken Sie auf **Erweiterte Ansicht**, um die erweiterte Ansicht mit Standard-ACLs, Maske und einer Beschreibung der „Superuser“ (Administratoren) anzuzeigen.  Auf diesem Blatt können Sie auch den Zugriff und die Standard-ACLs für untergeordnete Dateien und Ordner basierend auf den Berechtigungen des aktuellen Ordners rekursiv festlegen.

![Data Lake Store-ACLs](./media/data-lake-store-access-control/data-lake-store-show-acls-advance-view.png)

## <a name="the-super-user"></a>Administrator

Ein Administrator verfügt im Vergleich zu den anderen Benutzern in Data Lake Store über die meisten Berechtigungen. Für einen Administrator gilt Folgendes:

* Er besitzt RWX-Berechtigungen für **alle** Dateien und Ordner.
* Er kann die Berechtigungen für jede Datei und jeden Ordner ändern.
* Er kann den zuständigen Benutzer und die zuständige Gruppe einer beliebigen Datei und eines beliebigen Ordners ändern.

Ein Data Lake Store-Konto besitzt in Azure mehrere Azure-Rollen, z.B.:

* Besitzer
* Mitwirkende
* Leser

Alle Benutzer, die der Rolle **Besitzer** für ein Data Lake Store-Konto angehören, sind automatisch auch Administratoren für dieses Konto. Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung](../active-directory/role-based-access-control-configure.md).
Wenn Sie eine Rolle vom Typ „Benutzerdefinierte rollenbasierte Zugriffssteuerung“ (Role-Based Access Control, RBAC) mit Administratorberechtigungen erstellen möchten, müssen Sie dafür die folgenden Berechtigungen erteilen:
- Microsoft.DataLakeStore/accounts/Superuser/action
- Microsoft.Authorization/roleAssignments/write


## <a name="the-owning-user"></a>Der zuständige Benutzer

Der Benutzer, der das Element erstellt hat, ist automatisch der zuständige Benutzer für das Element. Der zuständige Benutzer hat folgende Möglichkeiten:

* Er kann die Berechtigungen einer Datei ändern, für die er als Besitzer fungiert.
* Er kann die zuständige Gruppe einer Datei ändern, für die er als Besitzer fungiert, solange der zuständige Benutzer auch der Zielgruppe angehört.

> [!NOTE]
> Der Besitzer kann einen anderen Besitzer einer Datei oder eines Ordners *nicht* ändern. Nur Administratoren können den zuständigen Benutzer einer Datei oder eines Ordners ändern.
>
>

## <a name="the-owning-group"></a>Die zuständige Gruppe

In den POSIX-Zugriffssteuerungslisten ist jeder Benutzer einer „primären Gruppe“ zugeordnet. So kann beispielsweise die Benutzerin „Alice“ der Gruppe „finance“ angehören. Alice kann außerdem mehreren Gruppen angehören, aber eine Gruppe wird immer als ihre primäre Gruppe festgelegt. In POSIX gilt: Wenn Alice eine Datei erstellt, wird die zuständige Gruppe der Datei auf ihre primäre Gruppe festgelegt (in diesem Fall „finance“).

Bei Erstellung eines neuen Dateisystemelements weist Data Lake Store der zuständigen Gruppe einen Wert zu.

* **1. Fall**: Der Stammordner „/“. Dieser Ordner wird erstellt, wenn ein Data Lake Store-Konto erstellt wird. In diesem Fall wird die zuständige Gruppe auf den Benutzer festgelegt, der das Konto erstellt hat.
* **2. Fall** (jeder andere Fall): Beim Erstellen eines neuen Elements wird die zuständige Gruppe aus dem übergeordneten Ordner kopiert.

Andernfalls verhält sich die zuständige Gruppe ähnlich wie zugewiesene Berechtigungen für andere Benutzer oder Gruppen.

Die zuständige Gruppe kann von folgenden Benutzern geändert werden:
* Beliebiger Administrator
* Zuständiger Benutzer, sofern er auch der Zielgruppe angehört

> [!NOTE]
> Die zuständige Gruppe kann die ACLs einer Datei oder eines Ordners *nicht* ändern.

## <a name="access-check-algorithm"></a>Algorithmus für die Zugriffsüberprüfung

Die folgende Abbildung zeigt den Zugriffsüberprüfungsalgorithmus für Data Lake Store-Konten:

![Data Lake Store-ACLs – Algorithmus](./media/data-lake-store-access-control/data-lake-store-acls-algorithm.png)


## <a name="the-mask-and-effective-permissions"></a>Die Maske und „effektive Berechtigungen“

Die **Maske** ist ein RWX-Wert, der verwendet wird, um beim Ausführen des Zugriffsüberprüfungsalgorithmus den Zugriff für **benannte Benutzer**, die **zuständige Gruppe** und **benannte Gruppen** einzuschränken. Im Anschluss werden die grundlegenden Konzepte für die Maske erläutert.

* Mit der Maske werden „effektive Berechtigungen“ erstellt. Dies bedeutet, dass die Berechtigungen zum Zeitpunkt der Zugriffsüberprüfung geändert werden.
* Die Maske kann direkt vom Dateibesitzer sowie von einem beliebigen Administrator bearbeitet werden.
* Mit der Maske können Berechtigungen entfernt werden, um die effektive Berechtigung zu erstellen. Mit der Maske können der effektiven Berechtigung aber *keine* Berechtigungen hinzugefügt werden.

Wir sehen uns nun einige Beispiele an. Im folgenden Beispiel ist die Maske auf **RWX** festgelegt. Durch die Maske werden also keine Berechtigungen entfernt. Die effektiven Berechtigungen für den benannten Benutzer, die zuständige Gruppe und die benannte Gruppe werden bei dieser Zugriffsüberprüfung nicht verändert.

![Data Lake Store-ACLs](./media/data-lake-store-access-control/data-lake-store-acls-mask-1.png)

Im folgenden Beispiel wird die Maske auf **R-X** festgelegt. Dies bedeutet, die Maske **deaktiviert die Schreibberechtigung** für **benannter Benutzer**, **zuständige Gruppe** und **benannte Gruppe** zum Zeitpunkt der Zugriffsüberprüfung.

![Data Lake Store-ACLs](./media/data-lake-store-access-control/data-lake-store-acls-mask-2.png)

Hier sehen Sie, wo die Maske für eine Datei oder einen Ordner im Azure-Portal angezeigt wird:

![Data Lake Store-ACLs](./media/data-lake-store-access-control/data-lake-store-show-acls-mask-view.png)

> [!NOTE]
> Bei einem neuen Data Lake Store-Konto wird die Maske für die Zugriffs-ACL des Stammordners („/“) standardmäßig auf „RWX“ festgelegt.
>
>

## <a name="permissions-on-new-files-and-folders"></a>Berechtigungen für neue Dateien und Ordner

Wenn unter einem bereits vorhandenen Ordner eine neue Datei oder ein erstellt wird, bestimmt die Standard-ACL des übergeordneten Ordners Folgendes:

- Eine Standard- und eine Zugriffs-ACL des untergeordneten Ordners
- Eine Zugriffs-ACL der untergeordneten Datei (Dateien besitzen keine Standard-ACL)

### <a name="the-access-acl-of-a-child-file-or-folder"></a>Die Zugriffs-ACL einer untergeordneten Datei oder eines untergeordneten Ordners

Beim Erstellen einer untergeordneten Datei oder eines untergeordneten Ordners wird die Standard-ACL des übergeordneten Elements als Zugriffs-ACL der untergeordneten Datei oder des untergeordneten Ordners kopiert. Falls ein **anderer** Benutzer in der Standard-ACL des übergeordneten Elements RWX-Berechtigungen besitzt, werden diese aus der Zugriffs-ACL des untergeordneten Elements entfernt.

![Data Lake Store-ACLs](./media/data-lake-store-access-control/data-lake-store-acls-child-items-1.png)

Die obigen Informationen zur Bestimmung der Zugriffs-ACL eines untergeordneten Elements sollten für die meisten Szenarien ausreichen. Wenn Sie dagegen mit POSIX-Systemen vertraut sind und sich im Detail über diese Transformation informieren möchten, lesen Sie den Abschnitt [Rolle von „umask“ beim Erstellen der Zugriffs-ACL für neue Dateien und Ordner](#umasks-role-in-creating-the-access-acl-for-new-files-and-folders) weiter unten in diesem Artikel.


### <a name="a-child-folders-default-acl"></a>Eine Standard-ACL des untergeordneten Ordners

Wenn unter einem übergeordneten Ordner ein untergeordneter Ordner erstellt wird, wird die Standard-ACL des übergeordneten Ordners unverändert als Standard-ACL des untergeordneten Ordners übernommen.

![Data Lake Store-ACLs](./media/data-lake-store-access-control/data-lake-store-acls-child-items-2.png)

## <a name="advanced-topics-for-understanding-acls-in-data-lake-store"></a>Weiterführende Themen zu ACLs in Data Lake Store

Im Anschluss finden Sie einige weiterführende Themen, in denen ausführlicher auf die Bestimmung von ACLs für Data Lake Store-Dateien oder -Ordner eingegangen wird.

### <a name="umasks-role-in-creating-the-access-acl-for-new-files-and-folders"></a>Rolle von „umask“ beim Erstellen der Zugriffs-ACL für neue Dateien und Ordner

Gemäß dem allgemeinen Konzept eines POSIX-kompatiblen Systems handelt es sich bei „umask“ um einen 9-Bit-Wert für den übergeordneten Ordner. Dieser dient dazu, die Berechtigung für **zuständiger Benutzer**, **zuständige Gruppe** und **andere Benutzer** für die Zugriffs-ACL einer neuen untergeordneten Datei oder eines neuen untergeordneten Ordners zu transformieren. Die Bits von „umask“ geben an, welche Bits in der Zugriffs-ACL des untergeordneten Elements deaktiviert werden sollen. Somit dient „umask“ also zur selektiven Unterbindung der Weitergabe von Berechtigungen für den **zuständigen Benutzer**, die **zuständige Gruppe** und **andere Benutzer**.

In einem HDFS-System handelt es sich bei „umask“ in der Regel um eine standortweite, von Administratoren gesteuerte Konfigurationsoption. Data Lake Store verwendet ein unveränderliches **umask-Element für das gesamte Konto**. Die folgende Tabelle enthält Informationen zum umask-Element für Data Lake Store.

| Benutzergruppe  | Einstellung | Auswirkung auf die Zugriffs-ACL eines neuen untergeordneten Elements |
|------------ |---------|---------------------------------------|
| zuständige Benutzer | ---     | Keine Auswirkungen                             |
| zuständige Gruppe| ---     | Keine Auswirkungen                             |
| anderer       | RWX     | Entfernen von Lesen, Schreiben und Ausführen         |

Die folgende Abbildung zeigt dieses umask-Element in Aktion. Letztendlich entfernt es **Lesen/Schreiben/Ausführen** für **andere** Benutzer. Da das umask-Element keine Bits für **zuständiger Benutzer** und **zuständige Gruppe** angibt, werden diese Berechtigungen nicht transformiert.

![Data Lake Store-ACLs](./media/data-lake-store-access-control/data-lake-store-acls-umask.png)

### <a name="the-sticky-bit"></a>Das Sticky Bit

Das Sticky Bit ist ein erweitertes Feature eines POSIX-Dateisystems. Im Kontext von Data Lake Store wird das Sticky Bit höchstwahrscheinlich nicht benötigt.

Die folgende Tabelle veranschaulicht die Funktionsweise des Sticky Bits in Data Lake Store:

| Benutzergruppe         | Datei    | Ordner |
|--------------------|---------|-------------------------|
| Sticky Bit **AUS** | Keine Auswirkungen   | Keine Auswirkungen.           |
| Sticky Bit **EIN**  | Keine Auswirkungen   | Sorgt dafür, dass nur **Administratoren** und der **zuständige Benutzer** eines untergeordneten Elements dieses Element löschen oder umbenennen können.               |

Das Sticky Bit wird im Azure-Portal nicht angezeigt.

## <a name="common-questions-about-acls-in-data-lake-store"></a>Allgemeine Fragen zu ACLs in Data Lake Store

Hier sind einige Fragen aufgeführt, die häufig zu ACLs in Data Lake Store gestellt werden.

### <a name="do-i-have-to-enable-support-for-acls"></a>Muss ich die Unterstützung für ACLs aktivieren?

Nein. Die ACL-basierte Zugriffssteuerung ist für ein Data Lake Store-Konto immer aktiviert.

### <a name="which-permissions-are-required-to-recursively-delete-a-folder-and-its-contents"></a>Welche Berechtigungen werden zum rekursiven Löschen eines Ordners und seines Inhalts benötigt?

* Der übergeordnete Ordner muss über **Schreib- und Ausführungsberechtigungen** verfügen.
* Der zu löschende Ordner und alle darin enthaltenen Ordner müssen über **Lese-, Schreib- und Ausführungsberechtigungen** verfügen.

> [!NOTE]
> Sie benötigen zum Löschen von Dateien in Ordnern keine Schreibberechtigungen. Außerdem gilt: Der Stammordner „/“ kann **nie** gelöscht werden.
>
>

### <a name="who-is-the-owner-of-a-file-or-folder"></a>Wer ist der Besitzer einer Datei oder eines Ordners?

Der Ersteller einer Datei oder eines Ordners wird als Besitzer festgelegt.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-folder-at-creation"></a>Welche Gruppe wird bei der Erstellung als zuständige Gruppe einer Datei oder eines Ordners festgelegt?

Die zuständige Gruppe wird aus der zuständigen Gruppe des übergeordneten Ordners kopiert, unter dem die neue Datei oder der neue Ordner erstellt wird.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Ich bin der zuständige Benutzer einer Datei, verfüge aber nicht über die erforderlichen RWX-Berechtigungen. Wie gehe ich vor?

Der zuständige Benutzer kann die Berechtigungen der Datei ändern und sich so selbst die erforderlichen RWX-Berechtigungen gewähren.

### <a name="when-i-look-at-acls-in-the-azure-portal-i-see-user-names-but-through-apis-i-see-guids-why-is-that"></a>Wenn ich mir die ACLs im Azure-Portal ansehe, sehe ich dort Benutzernamen, aber über APIs werden mir GUIDs angezeigt. Warum ist das so?

Einträge in den ACLs werden als GUIDs gespeichert, die Benutzern in Azure AD entsprechen. Die APIs geben die GUIDs unverändert zurück. Das Azure-Portal wandelt die GUIDs zur Vereinfachung der ACL-Verwendung nach Möglichkeit in benutzerfreundliche Namen um.

### <a name="why-do-i-sometimes-see-guids-in-the-acls-when-im-using-the-azure-portal"></a>Warum werden im Azure-Portal manchmal GUIDs in den ACLs angezeigt?

Eine GUID wird angezeigt, wenn der Benutzer in Azure AD nicht mehr vorhanden ist. Dies ist meist der Fall, wenn der Benutzer aus dem Unternehmen ausgeschieden ist oder sein Konto in Azure AD gelöscht wurde.

### <a name="does-data-lake-store-support-inheritance-of-acls"></a>Unterstützt Data Lake Store die Vererbung von ACLs?

Nein, aber Standard-ACLs können zum Festlegen von ACLs für untergeordnete Dateien und Ordner verwendet werden, die unter dem übergeordneten Ordner neu erstellt wurden.  

### <a name="what-is-the-difference-between-mask-and-umask"></a>Was ist der Unterschied zwischen „mask“ und „umask“?

| mask | umask|
|------|------|
| Die **mask** -Eigenschaft steht für jede Datei und jeden Ordner zur Verfügung. | **umask** ist eine Eigenschaft des Data Lake Store-Kontos. Data Lake Store enthält also nur ein einzelnes umask-Element.    |
| Die mask-Eigenschaft für eine Datei oder einen Ordner kann vom zuständigen Benutzer oder von der zuständigen Gruppe einer Datei oder aber vom Administrator geändert werden. | Die umask-Eigenschaft kann von keinem Benutzer geändert werden – auch nicht von einem Administrator. Hierbei handelt es sich um einen unveränderlichen, konstanten Wert.|
| Mithilfe der mask-Eigenschaft wird im Rahmen des Zugriffsüberprüfungsalgorithmus zur Laufzeit bestimmt, ob ein Benutzer zum Ausführen eines Vorgangs für eine Datei oder einen Ordner berechtigt ist. Die Rolle der Maske besteht in der Erstellung „effektiver Berechtigungen“ zum Zeitpunkt der Zugriffsüberprüfung. | „umask“ wird bei der Zugriffsüberprüfung überhaupt nicht verwendet. Mithilfe von „umask“ wird die Zugriffs-ACL neuer untergeordneter Elemente eines Ordners bestimmt. |
| Die Maske ist ein 3-Bit-RWX-Wert, der zum Zeitpunkt der Zugriffsüberprüfung auf den benannten Benutzer, die zuständige Gruppe und die benannte Gruppe angewendet wird.| Bei „umask“ handelt es sich um einen 9-Bit-Wert für den zuständigen Benutzer, die zuständige Gruppe und **andere Benutzer** eines neuen untergeordneten Elements.|

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Wo finde ich weitere Informationen zum POSIX-Zugriffssteuerungsmodell?

* [POSIX-Zugriffssteuerungslisten (ACLs) unter Linux](http://www.vanemery.com/Linux/ACL/POSIX_ACL_on_Linux.html)

* [HDFS Permission Guide](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html) (Handbuch zu HDFS-Berechtigungen)

* [POSIX FAQ (Häufig gestellte Fragen zu POSIX)](http://www.opengroup.org/austin/papers/posix_faq.html)

* [POSIX 1003.1 2008](http://standards.ieee.org/findstds/standard/1003.1-2008.html)

* [POSIX 1003.1 2013](http://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)

* [POSIX 1003.1 2016](http://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)

* [POSIX-ACL unter Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)

* [ACL using Access Control Lists on Linux](http://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/) (ACL mit Zugriffssteuerungslisten unter Linux)

## <a name="see-also"></a>Weitere Informationen

* [Übersicht über Azure Data Lake-Speicher](data-lake-store-overview.md)
