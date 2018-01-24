
Der Code für alle Funktionen in einer bestimmten Funktions-App befindet sich in einem Stammordner, der eine Hostkonfigurationsdatei und mindestens einen Unterordner enthält. Jeder Unterordner enthält den Code für eine separate Funktion, wie im folgenden Beispiel gezeigt:

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
```

Die Datei „host.json“ enthält die laufzeitspezifische Konfiguration und befindet sich im Stammordner der Funktions-App. Informationen zu den verfügbaren Einstellungen finden Sie in der [host.json-Referenz](../articles/azure-functions/functions-host-json.md).

Jede Funktion verfügt über einen Ordner, der mindestens eine Codedatei, die function.json-Konfiguration sowie weitere Abhängigkeiten enthält.

