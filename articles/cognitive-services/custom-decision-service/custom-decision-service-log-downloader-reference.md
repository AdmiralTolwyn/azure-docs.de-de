---
title: 'LogDownloader: Azure Cognitive Services | Microsoft Docs'
description: Herunterladen von Protokolldateien, die von Azure Custom Decision Service generiert werden.
services: cognitive-services
author: marco-rossi29
manager: marco-rossi29
ms.service: cognitive-services
ms.topic: article
ms.date: 05/09/2018
ms.author: marossi
ms.openlocfilehash: 783b534b3b3f4bb7f5d9f073f491690759edfea5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376963"
---
# <a name="logdownloader"></a>LogDownloader

Herunterladen von Protokolldateien, die von Azure Custom Decision Service generiert werden, und Generieren der *GZ*-Dateien, die zum Experimentieren verwendet werden.

## <a name="prerequisites"></a>Voraussetzungen

- Python 3: Installiert und in Ihrem Pfad vorhanden. Es wird empfohlen, die 64-Bit-Version zum Verarbeiten großer Dateien zu verwenden.
- Das Repository *Microsoft/mwt-ds*: [Klonen des Repositorys](https://github.com/Microsoft/mwt-ds).
- Das *azure-storage-blob*-Paket: Ausführliche Informationen zur Installation finden Sie unter [Microsoft Azure Storage-Bibliothek für Python](https://github.com/Azure/azure-storage-python#option-1-via-pypi).
- Geben Sie Ihre Azure-Speicherverbindungszeichenfolge in *mwt-ds/DataScience/ds.config* ein: Verwenden Sie die Vorlage *my_app_id: Eigene_Verbindungszeichenfolge*. Sie können mehrere `app_id` angeben. Wenn Sie `LogDownloader.py` ausführen und die Eingabe `app_id` in `ds.config` nicht gefunden wird, verwendet `LogDownloader.py` die `$Default`-Verbindungszeichenfolge.

## <a name="usage"></a>Verwendung

Navigieren Sie zu `mwt-ds/DataScience`, und führen Sie `LogDownloader.py` mit den entsprechenden Argumenten aus, wie im folgenden Code gezeigt:

```cmd
python LogDownloader.py [-h] -a APP_ID -l LOG_DIR [-s START_DATE]
                        [-e END_DATE] [-o OVERWRITE_MODE] [--dry_run]
                        [--create_gzip] [--delta_mod_t DELTA_MOD_T]
                        [--verbose] [-v VERSION]
```

### <a name="parameters"></a>Parameter

| Eingabe | BESCHREIBUNG | Standard |
| --- | --- | --- |
| `-h`, `--help` | Anzeigen der Hilfemeldung und Beenden. | |
| `-a APP_ID`, `--app_id APP_ID` | Die App-ID (d.h. der Name des Azure Storage-Blobcontainers). | Erforderlich |
| `-l LOG_DIR`, `--log_dir LOG_DIR` | Das Basisverzeichnis zum Herunterladen von Daten (es wird ein Unterordner erstellt).  | Erforderlich |
| `-s START_DATE`, `--start_date START_DATE` | Das Startdatum des Herunterladens (enthalten), im Format *JJJJ-MM-TT*. | `None` |
| `-e END_DATE`, `--end_date END_DATE` | Das Enddatum des Herunterladens (enthalten), im Format *JJJJ-MM-TT*. | `None` |
| `-o OVERWRITE_MODE`, `--overwrite_mode OVERWRITE_MODE` | Der zu verwendende Überschreibungsmodus. | |
| | `0`: Nie überschreiben. Benutzer fragen, ob zurzeit Blobs verwendet werden. | Standard | |
| | `1`: Benutzer zur Vorgehensweise befragen, wenn die Dateien unterschiedliche Größen aufweisen oder die Blobs zurzeit verwendet werden. | |
| | `2`: Immer überschreiben. Aktuell verwendete Blobs herunterladen. | |
| | `3`: Nie überschreiben und ohne Nachfrage anfügen, wenn die Größe größer ist. Aktuell verwendete Blobs herunterladen. | |
| | `4`: Nie überschreiben und ohne Nachfrage anfügen, wenn die Größe größer ist. Aktuell verwendete Blobs überspringen. | |
| `--dry_run` | Ausgeben, welche Blobs, heruntergeladen würden, ohne diese herunterzuladen. | `False` |
| `--create_gzip` | Erstellen einer *gzip*-Datei für Vowpal Wabbit. | `False` |
| `--delta_mod_t DELTA_MOD_T` | Das Zeitfenster (in Sekunden) zum Feststellen, ob eine Datei zurzeit verwendet wird. | `3600` Sek. (`1` Stunde) |
| `--verbose` | Ausgeben weiterer Details. | `False` |
| `-v VERSION`, `--version VERSION` | Die zu verwendende Version des Protokolldownloadprogramms. | |
| | `1`: Für nicht verarbeitete Protokolle (nur aus Gründen der Abwärtskompatibilität). | Veraltet |
| | `2`: Für verarbeitete Protokolle. | Standard |

### <a name="examples"></a>Beispiele

Verwenden Sie für einen Probelauf des Downloads aller Daten in Ihren Azure Storage-Blobcontainer den folgenden Code:
```cmd
python LogDownloader.py -a your_app_id -l d:\data --dry_run
```

Um nur Protokolle herunterzuladen, die seit dem 1. Januar 2018 mit `overwrite_mode=4` erstellt wurden, verwenden Sie den folgenden Code:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4
```

Zum Erstellen einer *gzip*-Datei, in der alle heruntergeladenen Dateien gemergt werden, verwenden Sie den folgenden Code:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4 --create_gzip
```